import tensorflow as tf
import tensorflow_hub as hub
import numpy as np
import pyautogui
import win32api, win32con, win32gui
import cv2
import math
import time
detector = hub.load("https://tfhub.dev/tensorflow/centernet/resnet50v1_fpn_512x512/1")
size_scale = 3
while True:
    # Get rect of Window
    hwnd = win32gui.FindWindow(None, 'Counter-Strike: Global Offensive')
    #hwnd = win32gui.FindWindow("UnrealWindow", None) # Fortnite
    rect = win32gui.GetWindowRect(hwnd)
    region = rect[0], rect[1], rect[2] - rect[0], rect[3] - rect[1]

    # Get image of screen
    image = np.array(pyautogui.screenshot(region=region))
    image = cv2.resize(image, (image.shape[1] // size_scale, image.shape[0] // size_scale))
    image = np.expand_dims(image, 0)
    ori_img = np.array(pyautogui.screenshot(region=region))
    ori_img = cv2.resize(ori_img, (ori_img.shape[1] // size_scale, ori_img.shape[0] // size_scale))
    image = np.expand_dims(ori_img, 0)
    img_w, img_h = image.shape[2], image.shape[1]

    # Detection
@@ -35,10 +36,12 @@
        # Choose only person(class:1)
        if classes[i] == 1 and scores[i] >= 0.5:
            ymin, xmin, ymax, xmax = tuple(box)
            if ymin > 0.5 and ymax > 0.8:
            if ymin > 0.5 and ymax > 0.8: # CS:Go
            #if int(xmin * img_w * 3) < 450: # Fortnite
                continue
            left, right, top, bottom = int(xmin * img_w), int(xmax * img_w), int(ymin * img_h), int(ymax * img_h)
            detected_boxes.append((left, right, top, bottom))
            #cv2.rectangle(ori_img, (left, top), (right, bottom), (255, 255, 0), 2)

    print("Detected:", len(detected_boxes))

@@ -71,4 +74,8 @@
        time.sleep(0.1)
        win32api.mouse_event(win32con.MOUSEEVENTF_LEFTUP, x, y, 0, 0)

    #ori_img = cv2.cvtColor(ori_img, cv2.COLOR_BGR2RGB)
    #cv2.imshow("ori_img", ori_img)
    #cv2.waitKey(1)

    time.sleep(0.1)
