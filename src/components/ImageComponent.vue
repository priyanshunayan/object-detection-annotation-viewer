<template>
<div>  
    <div class="image-container">
            <canvas v-for="(index) in length" :key="index" class="canvas-image" :id="index" v-on:click="fullscreen"> 
            </canvas>
    </div>
    <canvas id="fullScreen" @keyup.delete="close">
        <p>Hey tehre! </p>
    </canvas>
    </div>
</template>

<script>
import data from "../data/cars.json"
import paper from "paper"


export default {
        props: ['showWindows', 'showDoors', 'showWheels'],
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
                        const bboxes = this.extractBbox(image["id"]);
                        bboxes.forEach(bbox => {
                            if((bbox['name'] == "door" && this.showDoors) || (bbox['name'] == "wheels" && this.showWheels) || (bbox['name'] == "wheel" && this.showWheels) || (bbox['name'] == "window" && this.showWindows)){                            
                            const point1 = new paper.Point(bbox['bbox'][0]*ratioX, bbox['bbox'][1]*ratioY);
                            const point2 = new paper.Size(bbox['bbox'][2]*ratioX, bbox['bbox'][3]*ratioY);
                            const rectangle = new paper.Path.Rectangle(point1, point2);
                            const text = new paper.PointText(bbox['bbox'][0]*ratioX, bbox['bbox'][1]*ratioY);
                            text.justification = 'center';
                            text.fillColor = 'white';
                            text.strokeColor= "white";
                            text.content = `Object: ${bbox['name']} \n ConfidenceValue: ${Math.random().toFixed(2)} \n AnnotationSize: ${bbox['size']}px `;
                            text.visible = false;
                            text.fontFamily = 'Avenir';
                            text.justification = 'center';
                            rectangle.strokeColor = bbox['color'];
                            rectangle.opacity = 0.4;
                            rectangle.fillColor = bbox['color'];
                            const textRect = new paper.Path.Rectangle(text.bounds);
                            textRect.fillColor = 'black';
                            textRect.strokeColor = 'black';

                            text.insertAbove(textRect);
                            textRect.visible = false;
                            rectangle.onMouseEnter =  () => {// Layout the tooltip above the dot
                                text.visible = true;
                                textRect.visible = true;
                                rectangle.opacity = 0.8;
                                document.body.style.cursor = "pointer";

                            };
                            text.onMouseEnter = () => {
                                text.visible = true;
                                textRect.visible = true;
                                rectangle.opacity = 0.8;
                                document.body.style.cursor = "pointer";
                            }
                            rectangle.onMouseLeave = () => {
                                text.visible = false;
                                textRect.visible = false;
                                rectangle.opacity = 0.4;
                                document.body.style.cursor = "auto";
                            };
                            text.onMouseLeave = () => {
                                text.visible = false;
                                textRect.visible = false;
                                rectangle.opacity = 0.4;
                                document.body.style.cursor = "auto";
                            }
                            textRect.onMouseEnter = () => {
                                textRect.visible = true;
                                rectangle.opacity = 0.8;
                                document.body.style.cursor = "pointer";
                            }
                            textRect.onMouseLeave= () => {
                                textRect.visible = false;
                                rectangle.opacity = 0.4;
                                document.body.style.cursor = "auto";
                            }
                            }
                        });
            },
            fullscreen(event){
                const fullScreen = document.getElementById('fullScreen');
                fullScreen.style.display = "block";
                window.scrollTo(0,0)
                this.paintSingleImage(event.srcElement.id, true);
                const cans = document.getElementsByClassName('canvas-image');
                cans.forEach(can => {
                    can.style.display = "none";
                });          
                },
            close(){

                const fullScreen = document.getElementById('fullScreen');
                fullScreen.style.display = "none";
                const cans = document.getElementsByClassName('canvas-image');
                cans.forEach(can => {
                    can.style.display = "flex";
                });
            },
            paintSingleImage(id, showText=false){
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
                    if(showText){
                     const text = new paper.PointText(window.innerWidth/2,window.innerHeight-10);
                            text.justification = 'center';
                            text.fillColor = 'white';
                            text.strokeColor= "white";
                            text.content = `Press any key to close`;
                            text.visible = true;
                            text.fontFamily = 'Avenir';
                            text.justification = 'center';
                            text.fontSize = '1.5em'
                            const textRect = new paper.Path.Rectangle(text.bounds);
                            textRect.fillColor = '#898989';
                            textRect.strokeColor = '#898989';
                            text.insertAbove(textRect);
                    }

                    raster.onLoad = () => {
                        raster.position = paper.view.center;
                        raster.size = paper.view.size;
                    }
                    this.paintBoxes(image, ratioX, ratioY)
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
                    res["size"] = filter['area'];
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
            showDoors: function(){
                this.paintImages()
            },
            showWindows: function(){
                this.paintImages()
            },
            showWheels: function(){
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
        margin: 0 auto;
        align-items: center;
        justify-content: center;
    }
    .canvas-image{
        margin: 0.5em;
        border-radius: 5px;
        opacity: 0.8;
        transition: opacity .3s ease-out
    }
    .pointer{
        cursor: pointer;
    }
    .canvas-image:hover{
        opacity:1;
    }
    #closeInstruction{
        position: absolute;
        bottom: 0;
        left:0;
        font-size: 1.5em;
    }
    #fullScreen{
        position: absolute;
        top:0;
        left: 0;

    }
</style>