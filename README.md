from moviepy.editor import *
from moviepy.video.fx import fadein, fadeout

# ============================================================
# CONFIGURATION
# ============================================================
VIDEO_WIDTH = 1080
VIDEO_HEIGHT = 1920  # Vertical for TikTok/Reels
BACKGROUND_COLOR = (20, 25, 35)

# ============================================================
# YOUR CODE WITH MARYAM
# ============================================================
CODE_LINES = [
    ("# happy_birthday.py", '#6A9955'),
    ("", None),
    ('birthday = "Happy Birthday to you,"', '#F9A825'),
    ("", None),
    ("def main():", '#C586C0'),
    ('    """Display a greeting message."""', '#6A9955'),
    ('    name = "Maryam"', '#4FC1FF'),
    ("    print(birthday)", '#4FC1FF'),
    ('    print(f"Happy Birthday {name}!")', '#4FC1FF'),
    ('    print("I hope this message finds you well.")', '#4FC1FF'),
    ('    print("You have successfully completed the course.")', '#4FC1FF'),
    ("", None),
    ("if __name__ == '__main__':", '#C586C0'),
    ("    main()", '#4FC1FF'),
]

OUTPUT_LINES = [
    "Happy Birthday to you,",
    "Happy Birthday Maryam!",
    "I hope this message finds you well.",
    "You have successfully completed the course.",
]

# ============================================================
# VIDEO GENERATION
# ============================================================
def create_text_clip(text, color, x, y, size=32, stroke=2):
    """Create a text clip."""
    if not text:
        return None
    clip = TextClip(
        text,
        fontsize=size,
        color=color,
        font='Courier',
        stroke_color='black',
        stroke_width=stroke,
        method='caption',
        size=(850, None),
        interline=-3
    )
    return clip.set_position((x, y))

def create_frame(code_lines, show_output=False):
    """Create a single frame."""
    clips = []
    
    # Code text
    code_texts = [line[0] for line in code_lines]
    full_code = "\n".join(code_texts)
    
    # Line numbers
    nums = "\n".join([str(i+1) for i in range(len(code_texts))])
    num_clip = create_text_clip(nums, '#858585', 40, 100, 28, 1)
    if num_clip:
        clips.append(num_clip)
    
    # Code
    code_clip = create_text_clip(full_code, '#FFFFFF', 90, 100, 32, 2)
    if code_clip:
        clips.append(code_clip)
    
    # Output
    if show_output:
        sep = create_text_clip("─" * 60, '#404040', 90, 900, 24, 1)
        if sep:
            clips.append(sep)
        
        title = create_text_clip("▶ OUTPUT:", '#FF9800', 90, 950, 28, 1)
        if title:
            clips.append(title)
        
        output_text = "\n".join(OUTPUT_LINES)
        out = create_text_clip(output_text, '#7EE787', 90, 1000, 32, 2)
        if out:
            clips.append(out)
    
    return CompositeVideoClip(clips) if clips else None

# ============================================================
# MAIN
# ============================================================
print("🎬 Generating birthday video for Maryam...")
print("=" * 50)

all_clips = []

# Step 1: Typing animation
print("⌨️  Typing animation...")
for i in range(len(CODE_LINES)):
    frame = create_frame(CODE_LINES[:i+1], show_output=False)
    if frame:
        frame = frame.set_duration(1.0)
        all_clips.append(frame)

# Step 2: Show output
print("💻  Showing output...")
final_frame = create_frame(CODE_LINES, show_output=True)
if final_frame:
    final_frame = final_frame.set_duration(5)
    all_clips.append(final_frame)

# Step 3: Celebration
print("🎂  Celebration screen...")
birthday = TextClip(
    "🎂 Happy Birthday Maryam! 🎂",
    fontsize=70,
    color='gold',
    font='Arial',
    stroke_color='black',
    stroke_width=3
).set_position('center').set_duration(5)

subtitle = TextClip(
    "Made with ❤️ using Python",
    fontsize=35,
    color='white',
    font='Arial'
).set_position(('center', 800)).set_duration(5)

for clip in [birthday, subtitle]:
    clip = fadein.fadein(clip, 0.5)
    clip = fadeout.fadeout(clip, 0.5)
    all_clips.append(clip)

# Combine
print("🔄  Combining clips...")
video = concatenate_videoclips(all_clips)

# Background
bg = ColorClip(
    size=(VIDEO_WIDTH, VIDEO_HEIGHT),
    color=BACKGROUND_COLOR,
    duration=video.duration
)

final_video = CompositeVideoClip([bg, video.set_position('center')])

# Export
print("💾  Exporting video... (1-2 minutes)")
output_file = "happy_birthday_maryam.mp4"

final_video.write_videofile(
    output_file,
    fps=24,
    codec='libx264',
    audio_codec='aac',
    threads=4,
    verbose=False,
    logger=None
)

print("=" * 50)
print(f"✅ Video created: {output_file}")
print(f"⏱️  Duration: {final_video.duration:.1f} seconds")
print("=" * 50)
