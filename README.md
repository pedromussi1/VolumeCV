
<h1 align="center">VolumeCV</h1>

<p align="center">
  <a href="https://www.youtube.com/watch?v=h8sp7vFeV7c"><img src="https://i.imgur.com/364Z23k.gif" alt="YouTube Demonstration" width="800"></a>
</p>

<h2>Description</h2>

<p>The goal of this project was to develop a program that used AI to recognize human hands through feedback of a webcamera and use that to control audio volume of the computer. This worked by identifying points in the hand for discerning between different fingers, and using that information to change the volume of the computer as we judged best.</p>

<h2>Languages and Utilities Used</h2>

<ul>
  <li><b>Python</b></li>
  <li><b>Mediapipe</b></li>
  <li><b>OpenCV</b></li>
</ul>

<h2>Environments Used</h2>

<ul>
  <li><b>Windows 11</b></li>
  <li><b>PyCharm</b></li>
</ul>

<h2>
<a href="https://github.com/pedromussi1/MyMovieList/blob/main/READCODE.md">Code Breakdown Here!</a>
</h2>

<h2>Project Walk-through</h2>

<p>Download files, install OpenCV and mediapipe into Python Interpreter. Run main.py file.</p>

<h3>Hand Tracking</h3>

<p align="center">
  <kbd><img src="https://ai.google.dev/static/edge/mediapipe/images/solutions/hand-landmarks.png" alt="HandTracking"></kbd>
</p>

<p>The first step is to detect hands in the image and draw landmarks of that hands so that data can be compared and manipulated. THis is possible with the help of a technology developed by Google called MediaPipe Hand Landmarker. NOt only does this technology perfectly detects hands, it also adds unique points to each joint and enumerates them.  </p>

<h3>Manipulating Landmarks</h3>

<p align="center">
  <kbd><img src="https://i.imgur.com/vz5C8Se.png" alt="VolumeSetting"></kbd>
</p>

<p>By finding the position of each point of the hand, the program knows how to differentiate between thumb, index, middle, ring, and pinky fingers. Using this information we can create a consistent functionality for the program. Since there are multiple points in each finger, x and y axis of a finger can also be determined, thus enabling us to know if a finger is "up" or "down".</p>

<h3>Volume Setting</h3>

<p align="center">
  <kbd><img src="https://i.imgur.com/vz5C8Se.png" alt="VolumeSetting"></kbd>
</p>

<p>In our case, here is the logic I have implemented: the distance of the thumb and index finger of a hand will be calculated. The farther they are from each other, the higher the volume will be set. In order for the computer volume to be changed to said volume, the hand's pinky must be "down".</p>

<h3>Adding movie title</h3>

<p align="center">
  <kbd><img src="https://i.imgur.com/0n9B35x.png" alt="AddingMovie"></kbd>
</p>

<p>After logging in the user will see this page, where they can add movie titles to their list and give a rating to each one.</p>

<p align="center">
  <kbd><img src="https://i.imgur.com/NpJRsFX.gif" alt="DeletingItem" width="1000"></kbd>
</p>

<p>By typing the title of a movie in the input field, a drop-down list of movies will be shown. The user can select of the the shown titles, type a rating, and add the movie to the list. If a movie is not selected from the drop-down, the user cannot add it to the list.</p>
<p>The 'Edit' button provides a chance for the user to changetheir rating for a movie they have previously added to the list. After pressing 'Save' the rating will be updated. THe user can also press 'Delete' if they want to remove an item from the list.</p>

<h3>Different Users</h3>

<p align="center">
  <kbd><img src="https://i.imgur.com/7lvQk5H.png" alt="AddingMovie"></kbd>
</p>

<p>In case you decide to have two different accounts, you will notice that they do not share the same list. Each user has their own list and all the rules of the list such as no two entries can have the same name are applicable for each user account.</p>

<p align="center">
  <kbd><img src="https://i.imgur.com/emqUmc0.png" alt="AddingMovie"></kbd>
</p>
