<template>
    <div>
        <canvas id="three"></canvas>
    </div>
</template>

<script>
import * as THREE from "three";
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader.js";//引入GLTF文件的渲染器

const resizeRendererToDisplaySize = function (renderer) {
    const canvas = renderer.domElement;//从渲染器处获取canvas的DOM对象，不知道和直接用DOM操作获取的对象有什么区别
    let width = window.innerWidth;
    let height = window.innerHeight;
    let canvasPixelWidth = canvas.width / window.devicePixelRatio;
    let canvasPixelHeight = canvas.height / window.devicePixelRatio;

    const needResize = canvasPixelWidth !== width || canvasPixelHeight !== height;//canvas像素大小其实不一定与显示设备大小匹配，故利用设备像素大小与canvas像素大小之比与画布宽高算出与设备适合的canvas像素构成的宽高，再将其与设备宽高比较，判断是否需要renderer修改canvas像素大小
    //🤣还是没懂这里要干嘛
    if (needResize) {
        renderer.setSize(width, height, false);
    }
    return needResize;
}

const initThree = function () {
    const scene = new THREE.Scene();//创建场景对象
    scene.background = new THREE.Color("#FFF");//为场景添加背景，vscode里的色彩插件居然不能适配字符串你的颜色😓

    const canvas = document.querySelector("#three");//获取canvas的DOM对象
    const renderer = new THREE.WebGLRenderer({ canvas, antialias: true });//创建webgl渲染器对象并将canvas对象作为参数，antialias: true为打开抗锯齿，默认关闭
    const camera = new THREE.PerspectiveCamera(50, window.innerWidth / window.innerHeight, 0.1, 1000);//创建透视相机，透视相机效果类似人眼；50为视野广度(fov)；window.innerWidth/Height引用了浏览器窗口大小以用于视锥体横纵比(aspect)；0.1为相机近面，在近面后包括近面的对象将不被渲染；1000为相机远面，远面以前的对象将不会被渲染
    camera.position.z = 10;//设置相机z坐标
    
    const gltfLoader = new GLTFLoader();//创建gltf渲染器对象；glb是二进制文件，gltf是可编辑文件，都能用
    gltfLoader.load("ball.glb",
        (gltf) => {
            let model = gltf.scene;//解构一下，这里有可能对模型的纹理进行操作
            scene.add(model);
        }, //资源加载结束运行
        (xhr) => console.log((xhr.loaded / xhr.total * 100) + '% loaded'),//❓
        (error) => console.log(`寄：${error}`)
    );

    function animate() {//动画帧函数
        renderer.render(scene, camera);//渲染器绑定场景和相机
        resizeRendererToDisplaySize(renderer);
        requestAnimationFrame(animate);//使动画帧绑定动画帧函数，😓之前还疑问这里有回调地狱
    }
    animate();//走！
}

export default {
    name: 'ThreeIndex',//eslint-plugin-vue要求组件名称必须要是多词，我真的是绝绝子
    mounted() {
        initThree()
    }
}

</script>

<style scoped>
#three {
    width: 100%;
    height: 100%;
    position: fixed;
    /*绝对定位，相对于<body>*/
    left: 0;
    top: 0;
}
</style>