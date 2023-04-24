<template>
    <div id="canvas"></div>
</template>
<script>
import { Renderer, Camera, Geometry, Program, Mesh } from "ogl";
import { vertex, fragment } from "../assets/scripts/glsl";
export default {
    name: "Background",
    mounted() {
        this.renderer = new Renderer({ depth: true, dpr: 2, alpha: true });
        this.gl = this.renderer.gl;
        this.canvas = this.gl.canvas;

        document.getElementById("canvas").appendChild(this.canvas);
        this.gl.clearColor(0, 0, 0, 0);

        this.camera = new Camera(this.gl, { fov: 15 });
        this.camera.position.z = 15;

        window.addEventListener("resize", this.resize, false);
        this.resize();

        for (let i = 0; i < this.particlesNumber; i++) {
            this.position.set([Math.random(), Math.random(), Math.random()], i * 3);
            this.random.set([Math.random(), Math.random(), Math.random(), Math.random()], i * 4);
        }

        this.geometry = new Geometry(this.gl, {
            position: { size: 3, data: this.position },
            random: { size: 4, data: this.random }
        });

        this.program = new Program(this.gl, {
            vertex,
            fragment,
            uniforms: {
                uTime: {
                    value: 0
                },
                uColor: {
                    value: "#0072ff"
                }
            },
            transparent: true,
            depthTest: false
        });

        const mesh = {
            mode: this.gl.POINTS,
            geometry: this.geometry,
            program: this.program
        }
        this.particles = new Mesh(this.gl, mesh);

        requestAnimationFrame(this.update);
    },
    data() {
        return {
            renderer: null,
            gl: null,
            canvas: null,
            camera: null,
            particlesNumber: 100,
            position: new Float32Array(100 * 3),
            random: new Float32Array(100 * 4),
            program: null,
            particles: null,
            geometry: null
        };
    },
    methods: {
        resize() {
            this.renderer.setSize(window.innerWidth, window.innerHeight);
            this.camera.perspective({ aspect: this.gl.canvas.width / this.gl.canvas.height });
        },
        update(t) {
            requestAnimationFrame(this.update);
            this.particles.rotation.z += 0.0025;
            this.program.uniforms.uTime.value = t * 0.00025;
            const render = {
                scene: this.particles,
                camera: this.camera
            }
            this.renderer.render(render);
        }
    }
};
</script>
