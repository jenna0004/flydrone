# flydrone
from dronekit import connect, VehicleMode, LocationGlobalRelative
import time
import cv2

# Connect to the vehicle
print("Connecting to vehicle...")
vehicle = connect('127.0.0.1:14550', wait_ready=True)

# Function to arm the drone and take off to a specified altitude
def arm_and_takeoff(aTargetAltitude):
    print("Arming motors")
    vehicle.mode = VehicleMode("GUIDED")
    vehicle.armed = True

    while not vehicle.armed:
        print("Waiting for arming...")
        time.sleep(1)

    print("Taking off!")
    vehicle.simple_takeoff(aTargetAltitude)

    while True:
        print("Altitude: ", vehicle.location.global_relative_frame.alt)
        if vehicle.location.global_relative_frame.alt >= aTargetAltitude * 0.95:
            print("Reached target altitude")
            break
        time.sleep(1)

# Function to move the drone to a specific location
def goto_position_target_global_int(lat, lon, alt):
    location = LocationGlobalRelative(lat, lon, alt)
    vehicle.simple_goto(location)

# Function for obstacle avoidance (simplified example)
def avoid_obstacles():
    print("Checking for obstacles...")
    # Placeholder for actual sensor integration and obstacle avoidance logic
    # This should be replaced with real sensor data processing

# Function to capture image using OpenCV
def capture_image():
    cap = cv2.VideoCapture(0)
    ret, frame = cap.read()
    if ret:
        cv2.imwrite('captured_image.jpg', frame)
        print("Image captured and saved as 'captured_image.jpg'")
    cap.release()

# Function to land the drone
def land_drone():
    print("Landing...")
    vehicle.mode = VehicleMode("LAND")
    while vehicle.armed:
        print("Waiting for landing...")
        time.sleep(1)
    print("Landed and disarmed")

# Main script
if __name__ == "__main__":
    try:
        arm_and_takeoff(10)  # Takeoff to 10 meters altitude

        # Move to a specific location
        goto_position_target_global_int(37.7749, -122.4194, 20)
        time.sleep(30)  # Wait for 30 seconds to reach the location

        # Avoid obstacles
        avoid_obstacles()

        # Capture an image
        capture_image()

        # Return to launch
        print("Returning to Launch")
        vehicle.mode = VehicleMode("RTL")

        # Land the drone
        land_drone()

    finally:
        # Close vehicle object before exiting script
        print("Close vehicle object")
        vehicle.close()
