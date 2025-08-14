from moviepy.editor import ImageClip
import numpy as np

# ==== SETTINGS ====
image_path = "your_photo.jpg"  # <-- Change to your file name
output_path = "eye_zoom.mp4"   # Output MP4 file
duration = 3                   # Seconds
eye_x_rel, eye_y_rel = 0.63, 0.37  # Relative coordinates of the eye (adjust if needed)
zoom_strength = 2.5            # Final zoom factor

# ==== CREATE CLIP ====
clip = ImageClip(image_path).set_duration(duration)

def crop_center_safe(get_frame, t):
    frame = get_frame(t)
    h, w, _ = frame.shape
    zoom_factor = 1 + (zoom_strength - 1) * (t / duration)
    crop_w, crop_h = max(w / zoom_factor, 2), max(h / zoom_factor, 2)
    center_x, center_y = w * eye_x_rel, h * eye_y_rel
    x1, x2 = int(np.clip(center_x - crop_w/2, 0, w)), int(np.clip(center_x + crop_w/2, 0, w))
    y1, y2 = int(np.clip(center_y - crop_h/2, 0, h)), int(np.clip(center_y + crop_h/2, 0, h))
    return frame[y1:y2, x1:x2]

zoomed_clip = clip.fl(crop_center_safe)

# ==== EXPORT ====
zoomed_clip.write_videofile(output_path, fps=30, codec="libx264", bitrate="2000k")
