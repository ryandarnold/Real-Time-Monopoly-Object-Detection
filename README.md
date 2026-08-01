# Real-Time Monopoly Game State Tracking
Final Product Demo: 

<!-- <img width="800" height="899" alt="592755542-e2855015-c8be-49ed-a7bb-49cd630075a1-ezgif com-video-to-gif-converter" src="https://github.com/user-attachments/assets/b175007e-a4a9-4a6d-aa12-a02bc4ee191b" /> -->

<p align="center">
  <img src="https://github.com/user-attachments/assets/b175007e-a4a9-4a6d-aa12-a02bc4ee191b"
       alt="Monopoly Demo"
       width="500">
</p>


**Highlights**
- Custom dataset of 240 4K images, 29 object classes, and 5,200+ manual annotations
- Trained 45 YOLOv5 models across multiple optimizers and hyperparameter configurations
- Achieved 0.944 mAP@0.5 and 0.826 mAP@0.5:0.95
- Implemented SIFT-based board orientation correction, contour-based board extraction, and gameplay state tracking


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

<p align="center">
  <img width="700" alt="image"
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
2) The Baseline dataset and up to +/- 15 degree rotation dataset ('Version 7 (V7)', with 539 images)
3) Baseline + rotation + up to 15 pixel blur ('Version 9 (V9)', with 540 images)

<h4><ins>Step 3) Training the model over my custom dataset </ins></h4>

Ultralytics’s YOLOv5s was trained over:

○ Batch size = [16, 8, 4, 2, 1]

○ Image resize = [640, 960, 1280, 1600, 1984]

○ Optimizers = [SGD, Adam, AdamW]

○ +29 other default hyperparameters


<h4><ins>Step 4) Run Inferencing and Implement Gameplay Logic </ins></h4>

Once the model was trained over my custom dataset, I ran inferencing and implemented the game state tracking logic. 

The game state tracking logic included cropping the board, realigning the board so its always upright on screen, keeping track of where each game piece was on the board, and determining if it had moved from its previous location: 

<p align="center">
  <img width="1500" alt="image"
  src="https://github.com/user-attachments/assets/31a591f3-ee6a-4077-9a9e-9824610f04a7">
  <br>
  <em>(Left) The cropped and rotated board. (Right) The board after running inferencing and recognizing each game piece</em>
</p>

<h4><ins> Results </ins></h4>
<p align="center">
  <img width="500" alt="image"
  src="https://github.com/user-attachments/assets/bde4a724-c2cb-405d-9ffb-e5d5616cca7d">
  <br>
  <em>Best dataset results</em>
</p>

Training the YOLOv5s model on the V7 dataset was shown to have the best mAP results, and thus this model was used to run inferencing and implement the gameplay logic.

<p align="center">
  <img width="1582" alt="image"
  src="https://github.com/user-attachments/assets/986e98a8-0971-42ff-9341-74fc2ca49b2e">
  <br>
  <em>(Left) The Precision-Recall Curve. (Right) The F1-Confidence Curve.</em>
</p>

<h4><ins>Difficulties encountered and their potential solutions </ins></h4>

1) Sometimes the model would say that it 'found' a game piece, when the game piece really wasn't there. Or, it would say it did NOT find a game piece that was actually there. This would happen every frame running at about 5FPS. One solution for this would be to keep a counter for each piece found. Only if a piece is found for 'x' number of frames in a row would it count as "found". A "not found" piece would need to be removed for 'y' number of frames in a row.

2) Inferencing is run every frame, and sometimes the model determines that the piece has 'moved' a few pixels, when it physical game piece didn't actually move. This causes an issue when trying to determine if a game piece moved, or money was exchanged. To combat this, you can try to find the average center of each game piece, and if the average center moved outside a certain radius 'r' after 'x' number of frames, then you can determine that the game piece moved

3) Dice values are often confused for each other. An ensemble method can be used, in which the main YOLO model would determine if the game piece was a dice at all, and then a separate model, trained on determining dice values, would then be used.

4) Since any player at any time can buy, sell or trade property to another player for any amount, it is not possible to verify the agreed-upon sell price for a property. Thus, a microphone and NLP must be employed.

5) Players don't normally have their property cards on the board, so board cropping may not be needed, but a maximum-distance-away distance from the board that property cards can be must be enforced. Also, players may hold their money in their hand away from the camera, and may exchange properties/money away from the camera. Again, adding a microphone and implementing NLP will probably lead to a solution.

6) Finally, all game pieces must be trained on, as only a subset of all game pieces are used to train the YOLOv5s model.


Full Product Demo: 

https://github.com/user-attachments/assets/e2855015-c8be-49ed-a7bb-49cd630075a1

