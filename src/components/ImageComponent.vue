<template>
<div>  
    <div class="image-container">
            <canvas v-for="(index) in length" :key="index" class="canvas-image" :id="index" v-on:click="fullscreen"> 
            </canvas>
    </div>
    <canvas id="fullScreen" @keyup.delete="close"></canvas>
    </div>
</template>

<script>
import data from "../data/cars.json"
import paper from "paper"
// import {onMounted} from "vue"

export default {
        props: ['checked'],
        data(){
            return {
                publicUrl: process.env.BASE_URL,
                images: data['images'],
                length: data['images'].length,
                annotations: data['annotations']
            }
        },
        methods:{
            paintBoxes(image, ratioX, ratioY){
                if(this.checked){
                        console.log(this.checked);
                        const bboxes = this.extractBbox(image["id"]);
                        bboxes.forEach(bbox => {                            
                            const point1 = new paper.Point(bbox['bbox'][0]*ratioX, bbox['bbox'][1]*ratioY);
                            const point2 = new paper.Size(bbox['bbox'][2]*ratioX, bbox['bbox'][3]*ratioY);
                            const rectangle = new paper.Path.Rectangle(point1, point2);
                            const text = new paper.PointText(bbox['bbox'][0]*ratioX, bbox['bbox'][1]*ratioY);
                            text.justification = 'center';
                            text.fillColor = 'black';
                            text.content = bbox['name'];
                            text.visible = false;
                            rectangle.strokeColor = bbox['color'];
                            rectangle.opacity = 0.4;
                            rectangle.fillColor = bbox['color'];
                            text.fontSize = "20px";
                            rectangle.onMouseEnter =  () => {// Layout the tooltip above the dot
                                text.visible = true;
                            };
                            rectangle.onMouseLeave = () => {
                                text.visible = false;
                            
                            };
                        });
                    }
            },
            fullscreen(event){
                console.log("i am clicked", event)
                const fullScreen = document.getElementById('fullScreen');
                fullScreen.style.display = "block";
                window.scrollTo(0,0)
                this.paintSingleImage(event.srcElement.id);
                const cans = document.getElementsByClassName('canvas-image');
                cans.forEach(can => {
                    can.style.display = "none";
                });          
                },
            close(){
                console.log("trying to close....")
                const fullScreen = document.getElementById('fullScreen');
                fullScreen.style.display = "none";
                const cans = document.getElementsByClassName('canvas-image');
                cans.forEach(can => {
                    can.style.display = "flex";
                });
            },
            paintSingleImage(id){
                    const image = this.images[id-1];
                    const canvas = document.getElementById("fullScreen"); 
                    canvas.width = window.innerWidth;
                    canvas.height = window.innerHeight;
                    const ratioX = window.innerWidth / image['width'];
                    const ratioY = window.innerHeight/ image['height'];
                    paper.setup(canvas);
                    const raster = new paper.Raster({
                            source: `${this.publicUrl}static/${image.file_name}`,
                    });
                    
                    raster.onLoad = () => {
                        raster.position = paper.view.center;
                        raster.size = paper.view.size;
                    }
                    this.paintBoxes(image, ratioX, ratioY)
                    console.log(id);
            },
            paintImages(){
                this.images.forEach((image, index) => {
                    const canvas = document.getElementById(String(index+1)); 
                    canvas.width = 500;
                    canvas.height = 500;
                    const ratioX = 500 / image['width'];
                    const ratioY = 500/ image['height'];
                    paper.setup(canvas);
                    const raster = new paper.Raster({
                            source: `${this.publicUrl}static/${image.file_name}`,
                    });
                    
                    raster.onLoad = () => {
                        raster.position = paper.view.center;
                        raster.size = paper.view.size;

                    }
                    this.paintBoxes(image, ratioX, ratioY);
                });

            },
            extractBbox(id){
                const result = [];
                const filtered = this.annotations.filter(annotation => annotation["image_id"] === id);
                filtered.forEach(filter => {
                    const res = {};
                    res['name'] = filter["metadata"]["name"];
                    res['bbox'] = filter["bbox"];
                    res["color"]= filter["color"];
                    result.push(res);
                });
                return result;
            },
            getPath(file_name){
                return `${this.publicUrl}static/${file_name}`
            },
        },
        mounted(){
            window.addEventListener('keypress', this.close)
            this.paintImages();
        },
        watch: {
            checked: function(){
                this.paintImages()
            }
        }
    }
</script>

<style lang="css" scoped>
    img{
        margin:0;
        padding:0;
    }
    .image-container {
        display: flex;
        width: 80%;
        flex-wrap: wrap;
        margin: 0 auto
    }
    #fullScreen{
        position: absolute;
        top:0;
        left: 0;

    }
</style>