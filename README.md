# PIANO
THIS IS BASIC CODE OF PYTHON IN WHICH WE USE CAMERA TO PLAY PIANO SOUND WITH THE HELP OF HAND'S FINGER.
WE USE DIFFERNT TYPE OF FUNCTION AND LIBRARY IN THIS CODE. 
author- PRINCE KUMAR


import cv2             //IMPORT CAMERA 
import mediapipe as mp
import pygame          // IMPORT PYGAME
import time            


pygame.mixer.init(frequency=44100, size=-16, channels=2, buffer=512)

// DOWNLOAD MUSIC AND SAVE IT TO THE FOLDER AND COPY THE LINK OF FILE AND PASTE IT
notes = {
    "index": r"C:\Users\risha\Downloads\piano_e.wav.wav",
    "middle": r"C:\Users\risha\Downloads\piano-d.wav.wav",
    "ring": r"C:\Users\risha\Downloads\piano_c.wav.wav",
    "pinky": r"C:\Users\risha\Downloads\68447_pinkyfinger_piano-g.wav",
  
}
     

mp_hands = mp.solutions.hands
mp_drawing = mp.solutions.drawing_utils

cap = cv2.VideoCapture(0)
cap.set(cv2.CAP_PROP_FRAME_WIDTH, 900)
cap.set(cv2.CAP_PROP_FRAME_HEIGHT, 900)


last_played = time.time()


with mp_hands.Hands(static_image_mode=False, max_num_hands=2, min_detection_confidence=0.5, min_tracking_confidence=0.5) as hands:
    while cap.isOpened():
        ret, frame = cap.read()
        if not ret:
            print("Failed to capture frame.")
            break

        frame = cv2.flip(frame, 1)
        rgb_frame = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB)
        results = hands.process(rgb_frame)

        raised_fingers = 0

        if results.multi_hand_landmarks:
            for hand_landmarks in results.multi_hand_landmarks:
                mp_drawing.draw_landmarks(frame, hand_landmarks, mp_hands.HAND_CONNECTIONS)

                def is_finger_raised(tip, dip):
                    return tip.y < dip.y

                index_finger_tip = hand_landmarks.landmark[mp_hands.HandLandmark.INDEX_FINGER_TIP]
                index_finger_dip = hand_landmarks.landmark[mp_hands.HandLandmark.INDEX_FINGER_DIP]
                middle_finger_tip = hand_landmarks.landmark[mp_hands.HandLandmark.MIDDLE_FINGER_TIP]
                middle_finger_dip = hand_landmarks.landmark[mp_hands.HandLandmark.MIDDLE_FINGER_DIP]
                ring_finger_tip = hand_landmarks.landmark[mp_hands.HandLandmark.RING_FINGER_TIP]
                ring_finger_dip = hand_landmarks.landmark[mp_hands.HandLandmark.RING_FINGER_DIP]
                pinky_finger_tip = hand_landmarks.landmark[mp_hands.HandLandmark.PINKY_TIP]
                pinky_finger_dip = hand_landmarks.landmark[mp_hands.HandLandmark.PINKY_DIP]
              
                if is_finger_raised(index_finger_tip, index_finger_dip):
                    raised_fingers += 1
                if is_finger_raised(middle_finger_tip, middle_finger_dip):
                    raised_fingers += 1
                if is_finger_raised(ring_finger_tip, ring_finger_dip):
                    raised_fingers += 1
                if is_finger_raised(pinky_finger_tip, pinky_finger_dip):
                    raised_fingers += 1
                 

                if time.time() - last_played > 0.5:
                    if raised_fingers == 1:
                        print("Playing index sound")
                        pygame.mixer.Sound(notes["index"]).play()
                        
                    elif raised_fingers == 2:
                        print("Playing middle sound")
                        pygame.mixer.Sound(notes["middle"]).play()
                        
                    elif raised_fingers == 3:
                        print("Playing ring sound")
                        pygame.mixer.Sound(notes["ring"]).play()
                        
                    elif raised_fingers == 4:
                        print("Playing pinky sound")
                        pygame.mixer.Sound(notes["pinky"]).play()

                    
                        
                    last_played = time.time()

        cv2.putText(frame, f"Raised Fingers: {raised_fingers}", (60, 50), cv2.FONT_HERSHEY_SIMPLEX, 1, (230, 0, 0), 2)
        cv2.imshow("Hand piano", frame)

        if cv2.waitKey(1) & 0xFF == ord('q'):
            break

cap.release()
cv2.destroyAllWindows()
