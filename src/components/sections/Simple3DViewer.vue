<template>
  <div class="simple-3d-viewer">
    <div ref="container" class="viewer-container"></div>
    <div class="controls">
      <el-button @click="resetView" size="small">Reset View</el-button>
      <el-button @click="loadModel" size="small" :loading="loading" v-if="!threeObjects.model">Reload</el-button>
    </div>
    <div class="info">
      <p>Status: {{ status }}</p>
      <p v-if="error" class="error">Error: {{ error }}</p>
    </div>
  </div>
</template>

<script>
import * as THREE from 'three'
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js'
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js'
import { markRaw } from 'vue'

export default {
  name: 'Simple3DViewer',
  props: {
    modelPath: {
      type: String,
      required: true
    }
  },
  data() {
    return {
      loading: false,
      status: 'Uninitialized',
      error: null
    }
  },
  setup() {
    // Use non-reactive ref to store Three.js objects, avoiding Vue proxy conflicts
    const threeObjects = {
      scene: null,
      camera: null,
      renderer: null,
      controls: null,
      model: null,
      animationId: null,
      lastMaxDim: null
    }
    
    return {
      threeObjects
    }
  },
  mounted() {
    this.initScene()
  },
  watch: {
    modelPath: {
      handler(newPath) {
        if (newPath && this.threeObjects.scene) {
          this.loadModel()
        }
      },
      immediate: true
    }
  },
  beforeUnmount() {
    this.cleanup()
  },
  methods: {
    initScene() {
      try {
        this.status = 'Initializing scene...'
        const container = this.$refs.container
        
        if (!container) {
          throw new Error('Container not found')
        }
        
        const width = container.clientWidth || 600
        const height = Math.round((container.clientWidth || 600) * 2 / 3)
        
        // Scene - use markRaw to avoid reactive proxy
        this.threeObjects.scene = markRaw(new THREE.Scene())
        this.threeObjects.scene.background = new THREE.Color(0xf0f0f0)
        
        // Camera
        this.threeObjects.camera = markRaw(new THREE.PerspectiveCamera(75, width / height, 0.1, 1000))
        this.threeObjects.camera.position.set(5, 5, 5)
        
        // Renderer
        this.threeObjects.renderer = markRaw(new THREE.WebGLRenderer({ antialias: true }))
        this.threeObjects.renderer.setPixelRatio(Math.min(window.devicePixelRatio || 1, 2))
        this.threeObjects.renderer.setSize(width, height)
        container.appendChild(this.threeObjects.renderer.domElement)
        
        // Controls
        this.threeObjects.controls = markRaw(new OrbitControls(this.threeObjects.camera, this.threeObjects.renderer.domElement))
        this.threeObjects.controls.enableDamping = true
        
        // Lighting - further enhanced brightness
        const ambientLight = markRaw(new THREE.AmbientLight(0x404040, 1.2))
        this.threeObjects.scene.add(ambientLight)
        
        const directionalLight = markRaw(new THREE.DirectionalLight(0xffffff, 1.5))
        directionalLight.position.set(10, 10, 5)
        this.threeObjects.scene.add(directionalLight)
        
        // Add multiple directional lights to enhance brightness
        const directionalLight2 = markRaw(new THREE.DirectionalLight(0xffffff, 1.0))
        directionalLight2.position.set(-10, 10, -5)
        this.threeObjects.scene.add(directionalLight2)
        
        const directionalLight3 = markRaw(new THREE.DirectionalLight(0xffffff, 0.8))
        directionalLight3.position.set(0, -10, 10)
        this.threeObjects.scene.add(directionalLight3)
        
        const directionalLight4 = markRaw(new THREE.DirectionalLight(0xffffff, 0.6))
        directionalLight4.position.set(0, 10, -10)
        this.threeObjects.scene.add(directionalLight4)
        
        // Add point lights to enhance local brightness
        const pointLight = markRaw(new THREE.PointLight(0xffffff, 0.8, 100))
        pointLight.position.set(5, 5, 5)
        this.threeObjects.scene.add(pointLight)
        
        const pointLight2 = markRaw(new THREE.PointLight(0xffffff, 0.8, 100))
        pointLight2.position.set(-5, 5, -5)
        this.threeObjects.scene.add(pointLight2)
        
        // Bind animate to ensure correct this context
        this.animate = this.animate.bind(this)
        this.animate()
        this.status = 'Scene initialization completed'
        
        // Auto load model
        if (this.modelPath) {
          this.loadModel()
        }
        
        // Handle resize
        this.onResize = () => {
          if (!this.$refs.container || !this.threeObjects.renderer || !this.threeObjects.camera) return
          const newWidth = this.$refs.container.clientWidth || 600
          const newHeight = Math.round(newWidth * 2 / 3)
          this.threeObjects.camera.aspect = newWidth / newHeight
          this.threeObjects.camera.updateProjectionMatrix()
          this.threeObjects.renderer.setSize(newWidth, newHeight)
        }
        window.addEventListener('resize', this.onResize)
        // Resize when container becomes visible or changes size
        if (typeof ResizeObserver !== 'undefined') {
          this.resizeObserver = new ResizeObserver(entries => {
            if (!this.$refs.container || !this.threeObjects.renderer || !this.threeObjects.camera) return
            const rect = entries[0].contentRect
            const w = Math.max(1, Math.floor(rect.width))
            const h = Math.max(1, Math.round(w * 2 / 3))
            this.threeObjects.camera.aspect = w / h
            this.threeObjects.camera.updateProjectionMatrix()
            this.threeObjects.renderer.setSize(w, h)
          })
          this.resizeObserver.observe(this.$refs.container)
        }
        // Ensure correct size after mount/layout
        this.onResize()

      } catch (error) {
        this.error = error.message
        this.status = 'Initialization failed'
        console.error('Scene initialization failed:', error)
      }
    },
    
    async loadModel() {
      if (!this.threeObjects.scene) {
        this.error = 'Scene not initialized'
        return
      }
      
      this.loading = true
      this.status = 'Loading model...'
      this.error = null
      
      try {
        console.log('Loading model:', this.modelPath)
        
        const loader = new GLTFLoader()
        const gltf = await new Promise((resolve, reject) => {
          loader.load(this.modelPath, resolve, undefined, reject)
        })
        
        // Clear previous model
        const previousModel = this.threeObjects.scene.getObjectByName('gltf-model')
        if (previousModel) {
          this.threeObjects.scene.remove(previousModel)
        }
        
        // Add new model
        this.threeObjects.model = markRaw(gltf.scene)
        this.threeObjects.model.name = 'gltf-model'
        
        // Calculate model size and adjust position
        const box = new THREE.Box3().setFromObject(this.threeObjects.model)
        const center = box.getCenter(new THREE.Vector3())
        const size = box.getSize(new THREE.Vector3())
        
        this.threeObjects.model.position.sub(center)
        
        this.threeObjects.scene.add(this.threeObjects.model)
        
        // Adjust camera position
        const maxDim = Math.max(size.x, size.y, size.z)
        this.threeObjects.lastMaxDim = maxDim
        const distance = maxDim * 1.5
        this.threeObjects.camera.position.set(distance, distance, distance)
        this.threeObjects.camera.lookAt(0, 0, 0)
        this.threeObjects.controls.target.set(0, 0, 0)
        this.threeObjects.controls.update()
        
        this.status = 'Model loaded successfully'
        console.log('Model loaded successfully')
        
      } catch (error) {
        this.error = error.message
        this.status = 'Model loading failed'
        console.error('Model loading failed:', error)
      } finally {
        this.loading = false
      }
    },
    
    resetView() {
      if (this.threeObjects.camera && this.threeObjects.controls) {
        const maxDim = this.threeObjects.lastMaxDim || 5
        const distance = maxDim * 1.5
        this.threeObjects.camera.position.set(distance, distance, distance)
        this.threeObjects.camera.lookAt(0, 0, 0)
        this.threeObjects.controls.target.set(0, 0, 0)
        this.threeObjects.controls.update()
      }
    },
    
    animate() {
      if (!this.threeObjects.renderer || !this.threeObjects.scene || !this.threeObjects.camera) return
      
      this.threeObjects.animationId = requestAnimationFrame(this.animate)
      
      if (this.threeObjects.controls) {
        this.threeObjects.controls.update()
      }
      
      this.threeObjects.renderer.render(this.threeObjects.scene, this.threeObjects.camera)
    },
    
    cleanup() {
      if (this.threeObjects.animationId) {
        cancelAnimationFrame(this.threeObjects.animationId)
      }
      if (this.onResize) {
        window.removeEventListener('resize', this.onResize)
      }
      if (this.resizeObserver) {
        this.resizeObserver.disconnect()
        this.resizeObserver = null
      }
      
      if (this.threeObjects.renderer && this.$refs.container) {
        this.$refs.container.removeChild(this.threeObjects.renderer.domElement)
        this.threeObjects.renderer.dispose()
      }
      if (this.threeObjects.controls) {
        this.threeObjects.controls.dispose()
      }
      
      // Reset all objects
      Object.keys(this.threeObjects).forEach(key => {
        this.threeObjects[key] = null
      })
    }
  }
}
</script>

<style scoped>
.simple-3d-viewer {
  padding: 20px;
}

.viewer-container {
  width: 100%;
  border: 1px solid #ccc;
  margin: 0;
}

.controls {
  text-align: center;
  margin-top: 10px;
}

.info {
  margin-top: 10px;
  text-align: center;
}

.error {
  color: red;
}
</style> 
