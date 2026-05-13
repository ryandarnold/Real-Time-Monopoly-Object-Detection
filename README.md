# Real-Time-Monopoly-Object-Detection

<h3><ins>Goal</ins></h3>

The goal of this project is to keep track of the game state of a live Monopoly board game being played by 2 players using a single top-down view camera.

<p align="center">
  <img width="450" alt="image"
  src="https://github.com/user-attachments/assets/ebd4cbd6-fa1a-482f-b2fa-e55bb13b28ec">
  <br>
  <em>Figure 2: Your caption here.</em>
</p>

<h3><ins>Why?</ins></h3>

Monopoly is one of the world’s most famous board games, and yet official in-person competitions are rarely held. I wanted to create a platform to address this disconnect by increasing the number of Monopoly competitions held. Automatic game state tracking would allow official competitions to be held without the fear of players cheating, thus not needing referees, and increasing the amount of competitions held!

<h3><ins>Difficulties</ins></h3>
Traditional CV techniques such as Template Matching, SIFT, Hough Transform, and Color Thresholding were deemed too slow, highly variant to lighting conditions, and/or ineffective altogether. It was determined that a computer vision machine learning-based approach would be necessary. 

However, upon researching, it was determined that there were no adequate Monopoly image datasets. Therefore, I needed to create my own unique dataset to train a YOLOv5s computer vision  model to recognize the game state. 

<p align="center">
  <img width="450" alt="Screenshot 2026-05-12 182025"
  src="https://github.com/user-attachments/assets/ea41a18c-3125-4290-b530-895b7fd68b9b">
  <br>
  <em>Figure 1: Main System Setup.</em>
</p>

Figure 1 includes the main Monopoly board game, 4K camera with overhead mount, and laptop running YOLOv5s trained on my [custom Monopoly board game image dataset](https://universe.roboflow.com/monopoly-board-game-pieces/monopoly-game-board-high-resolution-image-dataset).
