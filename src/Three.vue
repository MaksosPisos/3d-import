<template>
    <div ref="container" class="cursor-grab rounded-md" :class="{ 'cursor-grabbing': grabbing }"
        @mouseup="() => grabbing = false" @mousedown="() => grabbing = true">
        <div v-if="loading" class="spinner absolute">

        </div>
        <!-- <Loading class="absolute spinner"></Loading> -->
    </div>

</template>
<script>
import * as THREE from 'three'
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js';
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";
import Loading from './Loading.vue'

export default {
    components: {
    Loading,
    Loading
},
    name: 'Three',
    data() {
        return {
            cube: null,
            renderer: null,
            scene: null,
            camera: null,
            controls: null,
            loading: false,
            grabbing: false,
        }
    },
    methods: {
        init: function () {
            this.loading = true
            const container = this.$refs.container
            this.scene = new THREE.Scene()
            const loaderBG = new THREE.CubeTextureLoader();
            const texture = loaderBG.load([
                '../public/pos-x.jpg',
                '../public/neg-x.jpg',
                '../public/pos-y.jpg',
                '../public/neg-y.jpg',
                '../public/pos-z.jpg',
                '../public/neg-z.jpg',
            ]);
            this.scene.background = texture;
            this.camera = new THREE.PerspectiveCamera(
                75,
                600 / 600,
                0.1,
                1000
            )
            this.renderer = new THREE.WebGLRenderer()
            this.renderer.setSize(600, 600)
            container.appendChild(this.renderer.domElement)
            this.controls = new OrbitControls(this.camera, this.renderer.domElement);
            const light = new THREE.AmbientLight(0xffffff); // soft white light
            this.scene.add(light);
            // const geometry = new THREE.BoxGeometry(1, 1, 1)
            // const material = new THREE.MeshBasicMaterial({ color: 0x00ff00 })
            // this.cube = new THREE.Mesh(geometry, material)
            // this.scene.add(this.cube)

            this.camera.position.z = 5
            const loader = new GLTFLoader();

            loader.load('../public/adamHead.gltf', (gltf) => {
                this.scene.add(gltf.scene);
                this.loading = false;
            }, undefined, (error) => {
                console.error(error);
            });

            this.controls.update()
        },
        animate: function () {
            requestAnimationFrame(this.animate)
            // this.cube.rotation.x += 0.01
            this.controls.update()
            // this.cube.rotation.y += 0.01
            this.renderer.render(this.scene, this.camera)
        }
    },
    mounted() {
        this.init()
        this.animate()
    }
}
</script>

<style>
.spinner {
    top: calc(50% - 40px);
    left: calc(50% - 40px);
}
.spinner:after {
    animation: changeContent .8s linear infinite;
    display: block;
    content: "⠋";
    font-size: 80px;
    color: rgba(255, 255, 255, 0.814);
}

@keyframes changeContent {
    10% {
        content: "⠙";
    }

    20% {
        content: "⠹";
    }

    30% {
        content: "⠸";
    }

    40% {
        content: "⠼";
    }

    50% {
        content: "⠴";
    }

    60% {
        content: "⠦";
    }

    70% {
        content: "⠧";
    }

    80% {
        content: "⠇";
    }

    90% {
        content: "⠏";
    }
}
</style>