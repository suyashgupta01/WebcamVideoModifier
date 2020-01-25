import cv2
import matplotlib.pyplot as plt
import numpy as np

capture = cv2.VideoCapture(0)
if not capture.isOpened():  # if webcam cannot be opened
    raise IOError("Webcam cannot be opened!") 

angle = 0 # stores the current angle at which the picture is rotated
current_key = -1

while True:
    
    flag, frame = capture.read()
    
    c = cv2.waitKey(1) # returns -1 => c = -1 when user is not pressing any key

    if c != -1:
        current_key = c
    # waitKey(0) will display the window infinitely until any keypress (for image display).
    # waitKey(1) will display a frame for 1 ms, after which display will be automatically closed
    
    # colour = array of [b, g, r]
    if c == 27:                                                            # to exit esc
        break
    elif current_key == ord('1'):                                                    # remove blue = 1
        frame[:, :, 0] = 0 # slicing the 3d array ie: the image frame
    elif current_key == ord('2'):                                                    # remove green = 2
        frame[:, :, 1] = 0
    elif current_key == ord('3'):                                                    # remove red = 3
        frame[:, :, 2] = 0
    elif current_key == ord('n'):                                                    # negaitve effect = n
        frame = ~frame # complement kar dia
    elif current_key == ord('w'):                                                    # w/b (grayscale) = w
        frame = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
    elif current_key == ord('y'):                                                    # YUV Colour scheme = y
        frame = cv2.cvtColor(frame, cv2.COLOR_BGR2YUV)
    elif current_key == ord('h'):                                                    # HSV Colour scheme = h
        frame = cv2.cvtColor(frame, cv2.COLOR_BGR2HSV)
    elif current_key == ord('+') or  current_key == ord('-'):                        # rotate by 10 degrees 
        if current_key == ord('+') and c == ord('+'):                                            # + = to left
            angle += 10 
        elif current_key == ord('-') and c == ord('-'):                                          # - = to right
            angle -= 10                                     
        num_rows, num_cols = frame.shape[:2] 
        rot_kernal_matrix = cv2.getRotationMatrix2D((num_cols/2, num_rows/2), angle, 1.0)
        frame = cv2.warpAffine(frame, rot_kernal_matrix, (num_rows, num_cols))
    elif current_key == ord('b'):                                                    # blur = b
        num_rows, num_cols = frame.shape[:2] 
        blur_kernal_matrix = np.ones((10,10), dtype = np.float32 ) / 100.0
        frame = cv2.filter2D(frame, -1, blur_kernal_matrix)
    
    # now flipping the image :)
    frame = cv2.flip(frame, 1)
    # 0, for flipping the image around the x-axis (vertical flipping);
    # > 0 for flipping around the y-axis (horizontal flipping);
    # < 0 for flipping around both axes.
    
    # Resize the captured image to make it larger :)
    frame = cv2.resize(frame, None, fx=1.25, fy=1.25, interpolation=cv2.INTER_AREA) 
    
    cv2.imshow("Webcam Footage", frame) # "Webcam Footage" is the window title
    
capture.release() # release camera
cv2.destroyAllWindows() # to destroy all open windows after the our work's done