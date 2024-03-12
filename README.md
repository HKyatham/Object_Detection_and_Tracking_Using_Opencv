# Object Trajectory Detection and Tracking using Opencv.
The object mention is in a video. The object is thrown a some angle and thus follows a parabolic trajectory. The same can be seen in the video below.

[<img src="https://github.com/HKyatham/Trajectory_Detection_and_Tracking_Using_Opencv/blob/main/Video_and_Images/Object.png" width="50%">](https://www.youtube.com/watch?v=zM7O2KOo-vQ)

First read the video file using the opencv inbuilt functions.
```
cap=cv2.VideoCapture("object_tracking.mp4")
```

Read the video frame by frame and perform color to gray transformation on each frame using the opencv functions.
```
gImg=cv2.cvtColor(frame,cv2.COLOR_BGR2GRAY)
```

Since the object is black, try to find the pixel coordinates for black pixels and then find the min and max pixel coordinates of the object.
```
if(np.where(gImg==0)[0].size != 0 or np.where(gImg==0)[1].size != 0):
  array_y, array_x = np.where(gImg==0)
  x_min, x_max = np.amin(array_x),np.amax(array_x)
  y_min, y_max = np.amin(array_y),np.amax(array_y)
```

Find the centroid of the object using the min and max coordinates obtained.
```
centroids.append(((x_min+x_max)/2,(y_min+y_max)/2))
```

Then we try to fit a curve to few points from all the centroids(centroid of object in each frame)\
Since the curve in our case is parabola(an object thrown at a certain angle will follow parabolic trajectory)
We will use the equation y = ax2+bx+c.
we will try to substitute the few centroid points to find the a,b and c values.
Which can be done using he matrices. AX = B then X = inv(A) * B. Assume TOP LEFT corner of the frame as 0,0 and accordingly use ‘Standard Least Square’ to fit a curve (parabola)
```
A = np.array([[6.5**2, 6.5, 1],
              [1007**2, 1007, 1],
              [1895**2, 1895, 1]])
At = A.transpose()

B = np.array([[1010],[402.5],[1056]])

ATA = np.matmul(At,A)

invATA = np.linalg.inv(ATA)

invATAAt = np.matmul(invATA,At)

invATAAtB = np.matmul(invATAAt,B)

print(invATAAtB)
```

Once we get the constants a b and c values, we now know the trajectory equations.
Using image height and width as limits for y and x. We can try to generate the estimated centroids of the objects by substituting x values in the equation of parabola.
```
x = np.array([np.square(1000),1000,1])
y = np.matmul(x, invATAAtB)
```

Once all the estimated coordinates of the centroid of the object in each frame is obtained, we just have to take a frame and try to draw the curve using the cv2 inbuilt function.
```
cv2.polylines(polyFrame,[coords], False, (255,0,0), 3)
```

The same can be seen in the image below.
![Video_and_Images/Trajectory_in_Image.png](https://github.com/HKyatham/Trajectory_Detection_and_Tracking_Using_Opencv/blob/main/Video_and_Images/Trajectory_in_Image.png)
