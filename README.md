# Real-Time-Monopoly-Object-Detection

<h3><ins>Goal</ins></h3>

The goal of this project is to keep track of the game state of a live Monopoly board game being played by 2 players using a single top-down view camera. 

<img width="1200" alt="Screenshot 2026-05-12 201442" src="https://github.com/user-attachments/assets/ffbb5301-5307-41ef-997d-13c347764bf4" />


<h3><ins>Why?</ins></h3>

Monopoly is one of the world’s most famous board games, and yet official in-person competitions are rarely held. I wanted to create a platform to address this disconnect by increasing the number of Monopoly competitions held. Automatic game state tracking would allow official competitions to be held without the fear of players cheating, thus not needing referees, and increasing the amount of competitions held!

Organizations and/or players would purchase this setup to play in official competitions.

<h3><ins>Difficulties</ins></h3>
Traditional CV techniques such as Template Matching, SIFT, Hough Transform, and Color Thresholding were deemed too slow, highly variant to lighting conditions, and/or ineffective altogether. It was determined that a computer vision machine learning-based approach would be necessary. 

However, upon researching, it was determined that there were no adequate Monopoly image datasets. Therefore, I needed to create my own unique dataset to train a YOLOv5s computer vision  model to recognize the game state. 

<p align="center">
  <img width="450" alt="Screenshot 2026-05-12 182025"
  src="https://github.com/user-attachments/assets/ea41a18c-3125-4290-b530-895b7fd68b9b">
  <br>
  <em>Main system setup</em>
</p>

The main system setup includes the main Monopoly board game, 4K camera with overhead mount, and laptop running YOLOv5s trained on my [custom Monopoly board game image dataset](https://universe.roboflow.com/monopoly-board-game-pieces/monopoly-game-board-high-resolution-image-dataset). This setup was used for taking both the original dataset images as well as ran during inferencing. 

<h3><ins>System Design</ins></h3>

There are four main parts to this system: 

<h4><ins>Step 1) Picking an object detection technique</ins></h4>

<!-- <p align="center">
  <img width="315" alt="image"
  src="https://github.com/user-attachments/assets/6e96d233-fd9d-43cd-8cb8-04562c46c115">
  <br>
  <em>YOLOv5s characteristics</em>
</p> -->

<p align="center">
  <img width="1223" alt="image"
  src="https://github.com/user-attachments/assets/bccfb268-f5b0-4f23-9131-e2c92f63c005">
  <br>
  <em>YOLOv5s characteristics</em>
</p>


A processor powerful enough of running inferencing must be used, but almost must not be too expensive.
YOLOv5s was picked because it runs well on cheaper edge devices and still had a good Mean Average Precision (mAP) score.  

<h4><ins>Step 2) Create the image dataset</ins></h4>

I took 240 original Monopoly dataset images that included 29 classes and 5200+ manual annotations, and were annotated using Roboflow.

I created 3 training datasets to see which one had the highest mAP score:
1) A Baseline dataset ('Version 2 (V2)', with 240 images)
2) The Baseline dataset and up to +/- 15 degree rotation dataset ('Version 7 (V7), with 539 images)
3) Baseline + rotation + up to 15 pixel blur ('Version 9 (V9)', with 540 images)

<h4><ins>Step 3) Training the model over my custom dataset </ins></h4>

Ultralytics’s YOLOv5s was trained over:
○ Batch size = [16, 8, 4, 2, 1]
○ Image resize = [640, 960, 1280, 1600, 1984]
○ Optimizers = [SGD, Adam, AdamW]
○ +29 other default hyperparameters

<h4><ins>Step 4) Run Inferencing and Implement Gameplay Logic </ins></h4>

Once the model was trained over my custom dataset, I ran inferencing and implemented the game state tracking logic. 

The game state tracking logic included cropping the board, realigning the board so its always upright on screen, keeping track of where each 

<!-- This is a comment 
<h3><ins>Dataset</ins></h3>

The dataset included 

<h3><ins>Results</ins></h3>

The best training dataset and its graphs...

<h3><ins>What's next?</ins></h3>

<h3><ins>How the system worked (flow chart here?)</ins></h3>

what to do next...

-->
