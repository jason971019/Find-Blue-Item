# Find-Blue-Item
## Object
在 webcam 中選取最主要(面積最大)的藍色物件，並回傳其中心點位置座標(x, y)  
## Code
```Python
import cv2
import numpy as np

cap = cv2.VideoCapture(0)

bluelower=np.array([110,45,50])
blueupper=np.array([130,255,255])

while(1):
    ret, frame = cap.read()
    
    hsv = cv2.cvtColor(frame, cv2.COLOR_BGR2HSV)
    bluemask = cv2.inRange(hsv, bluelower, blueupper)
    bluemask1 = cv2.erode(bluemask, None, iterations=2)
    bluemask2 = cv2.dilate(bluemask1, None, iterations=2)
    blueres = cv2.bitwise_and(frame, frame, mask=bluemask2)

    _,contours,_ = cv2.findContours(bluemask2.copy(), cv2.RETR_EXTERNAL,cv2.CHAIN_APPROX_SIMPLE)

    c_max = []  
    max_area = 0  
    max_cnt = 0  
    for i in range(len(contours)):  
        cnt = contours[i]  
        area = cv2.contourArea(cnt)  
        if (area>max_area):  
            if(max_area!=0):  
                c_min = []  
                c_min.append(max_cnt)  
                cv2.drawContours(frame, c_min, -1, (0,0,0), cv2.FILLED)  
            max_area = area  
            max_cnt = cnt  
        else:  
            c_min = []  
            c_min.append(cnt)  
            cv2.drawContours(frame, c_min, -1, (0,0,0), cv2.FILLED) 
    
    c_max.append(max_cnt)
    
    M=cv2.moments(max_cnt)
    cX=int(M['m10'] / M['m00'])
    cY=int(M['m01'] / M['m00'])
    print('X= ',cX,' Y= ',cY)
    cv2.drawContours(frame, c_max, -1,(0,255,0), 2)
    cv2.imshow('frame', frame)
    
    k = cv2.waitKey(5)&0xFF
    if k == 27:
        break

cap.release()
cv2.destroyAllWindows()
```
## Result
![image](https://github.com/jason971019/Find-Blue-Item/blob/master/demo.png)
