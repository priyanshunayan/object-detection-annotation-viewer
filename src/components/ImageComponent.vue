<template>
<div>   
    <div class="image-container">
            <canvas v-for="(index) in length" :key="index" class="canvas-image" :id="index"> 
            </canvas>
    </div>
    </div>
</template>

<script>
import data from "../data/cars.json"
import paper from "paper"
// import {onMounted} from "vue"

export default {
        props: ['circle_diameter'],
        data(){
            return {
                x:20,
                y:20,
                publicUrl: process.env.BASE_URL,
                images: data['images'],
                length: data['images'].length,
                annotations: data['annotations']
            }
        },
        methods:{
            // updateDrawing(){
            //     const context = document.getElementById('main-canvas').getContext('2d');
            //     context.clearRect(0, 0, document.getElementById('main-canvas').width, document.getElementById('main-canvas').height);
            //     paper.setup(document.getElementById('main-canvas'))
            //     const point = new paper.Point(this.x, this.y)
            //     const circle = new paper.Path.Circle(point, this.circle_diameter/2)
            //     circle.fillColor = 'grey'
            //     circle.strokeColor = 'black'
            // },
            paintImage(){

                this.images.forEach((image, index) => {
                    //const ctx = document.getElementById(String(index+1)).getContext('2d');
                   
                    // img.src = `${this.publicUrl}static/${image.file_name}`;
                    const canvas = document.getElementById(String(index+1));
                    
                    canvas.width = 500;
                    canvas.height = 500;
                    const ratioX = 500 / image['width'];
                    const ratioY = 500/ image['height'];
                    paper.setup(canvas);
                    //console.log(canvas.width, canvas.height)
                    const raster = new paper.Raster({
                            source: `${this.publicUrl}static/${image.file_name}`,
                    });
                    
                    raster.onLoad = () => {
                        raster.position = paper.view.center;
                        raster.size = paper.view.size;

                    }
                    const bboxes = this.extractBbox(image["id"]);
                    bboxes.forEach(bbox => {                            
                            const point1 = new paper.Point(bbox['bbox'][0]*ratioX, bbox['bbox'][1]*ratioY);
                            const point2 = new paper.Size(bbox['bbox'][2]*ratioX, bbox['bbox'][3]*ratioY);
                            const rectangle = new paper.Path.Rectangle(point1, point2);
                            rectangle.on('click', () => {
                                console.log(bbox['name'], "hovered");
                            })
                            rectangle.fillColor = bbox['color'];
                            rectangle.strokeColor = 'black';
                        })    
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
            boundLabel(){
                // this.images.forEach((image, index) => {
        
                //         const point1 = new paper.Point(0, 0);
                //         console.log(point1);
                //         const point2 = new paper.Point(100, 100);
                //         console.log(point2);
                //         const rectangle = new paper.Path.Rectangle(point1, point2)
                //         rectangle.fillColor = 'grey'
                //         rectangle.strokeColor = 'black'
                    
                // });


            },
            getPath(file_name){
                return `${this.publicUrl}static/${file_name}`
            },
        },
        mounted(){
            this.paintImage();
            //this.boundLabel();
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
</style>