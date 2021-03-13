import numpy as np
import cv2
import serial
import os
import Tkinter
from PIL import Image, ImageTk

#init serial strings
xMeasure = "x"
yMeasure = "y"
Instruction = "Select Command"

xTare = 0.0
yTare = 0.0
xTareStore = 0.0
yTareStore = 0.0

#detects if webcam is plugged in.  Only works with linux, only works if onboard webcam exists
if os.path.exists('/dev/video1') == 1:
    cap = cv2.VideoCapture(1)
else:
    cap = cv2.VideoCapture(0)
    
#init serial port connection. Detects if USB device is plugged in. Isn't triggered by webcam. Only works with linux.
if os.path.exists('/dev/ttyACM0') == 1:
    port = '/dev/ttyACM0'
    ard = serial.Serial(port,115200,timeout=None)
else:
    port = 'Signal Not Found'
    
#init crosshairs
HLine = np.ones((1,1))
VLine = np.ones((1,1))

#Find frame height and width, find midway points
FrameWidth = cap.get(cv2.cv.CV_CAP_PROP_FRAME_WIDTH)
FrameWidth = int(FrameWidth)
FrameHeight = cap.get(cv2.cv.CV_CAP_PROP_FRAME_HEIGHT)
FrameHeight = int(FrameHeight)
HalfWidth = FrameWidth / 2
HalfHeight = FrameHeight / 2

#initiate the main window
root = Tkinter.Tk()
root.title("Optical")
root.bind('<Escape>', lambda e: root.quit())
lmain = Tkinter.Label(root)
lmain.grid(row=1, rowspan=40, column=1)

#init main video loop
def show_frame():
    global xMeasure
    global yMeasure
    global xTare
    global yTare
    global Serial
    global xTareStore
    global yTareStore
    
    #init video capture, flip image
    ret, frame = cap.read()
    frame = cv2.flip(frame, 1)
    #use grayscale
    gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
    
    #display crosshairs
    VLine = cv2.line(gray,(HalfWidth,0),(HalfWidth,FrameHeight),(0,0,0),1)
    HLine = cv2.line(gray,(0,HalfHeight),(FrameWidth,HalfHeight),(0,0,0),1)
    
    #separate the serial signal into x and y values
    if port == 'Signal Not Found':
        xMeasure = port
        yMeasure = port
    else:
        Serial = ard.readline()
        print Serial
        if "x" in Serial:
            try:
                xTareStore = float(Serial[1:])*.000983263993948562783661119515885
                xMeasure = round(float(Serial[1:])*.000983263993948562783661119515885-xTare,3)
            except:
                xMeasure = 0
            xMeasure = str(xMeasure)
        elif "y" in Serial:
            try:
                yTareStore = float(Serial[1:])*.000191
                yMeasure = float(Serial[1:])*.000191-yTare
                ard.flushInput()
            except:
                yMeasure = 0
            yMeasure = str(yMeasure)
        else:
            xMeasure = "Incompatable Value"
            yMeasure = "Incompatable Value"
    
    
    #display values on screen
    font = cv2.FONT_HERSHEY_PLAIN
    cv2.putText(gray,xMeasure,(10,FrameHeight-50), font, 1, 255)
    cv2.putText(gray,yMeasure,(10,FrameHeight-30), font, 1, 255)
    cv2.putText(gray,Instruction,(10,FrameHeight-10), font, 1, 255)
    
    #wraps openCV video in Tkinter
    img = Image.fromarray(gray)
    imgtk = ImageTk.PhotoImage(image=img)
    lmain.imgtk = imgtk
    lmain.configure(image=imgtk)
    lmain.after(1, show_frame)
    
    
    
show_frame()

def Store():
    print "value not stored"
    
Store = Tkinter.Button(root, text="Store Value", command=Store)
Store.grid(row=1, column=2, ipadx=20)

def getArc():
    print "arc not calculated"
    
getArc = Tkinter.Button(root, text="Calculate Arc radius", command=getArc)
getArc.grid(row=2, column=2)

def tareX():
    global xTare
    global xTareStore
    global Serial
    
    xTare = xTareStore
    
tareX = Tkinter.Button(root, text="Tare X", command=tareX)
tareX.grid(row=3, column=2)

def tareY():
    global yTare
    global yTareStore
    global Serial    
    
    yTare = yTareStore
    
tareY = Tkinter.Button(root, text="Tare Y", command=tareY)
tareY.grid(row=4, column=2)

root.mainloop()
cap.release()
cv2.destroyAllWindows()
