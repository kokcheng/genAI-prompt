# 📦 Sticker Creation Guide for Telegram & WhatsApp

This guide explains how to generate a 4×4 sticker sheet using **Gemini**, split it into 16 individual stickers using Python, and finally upload them to **Telegram** and **WhatsApp**.

---

## 🖼️ 1. Generate a 4×4 Sticker Sheet in Gemini

Use the following prompt in **Google Gemini** (or any AI image generator) to create your sticker sheet:

Please use the characters from the attached image to create 16 chibi-style, colorful LINE stickers. The stickers should be diverse, featuring various everyday actions and cool expressions or phrases.

Each sticker character should be placed within a circular background, and the 16 designs must be arranged in a 4×4 grid. The final image should be 2048 × 2048 pixels, with equal white space around each sticker. Ensure that each character is easy to separate into 16 individual images after generation, and that the overall canvas has equal width and height.



### 👉 Generated Sticker Sheet  
*(Insert your generated 2048×2048 image here)*  
`<ADD_YOUR_GENERATED_IMAGE_HERE>`

---

## ✂️ 2. Split the Sticker Sheet into 16 Images Using Python

Save the generated image (2048 × 2048) as **stickers.png**, then run the following script:

```python
from PIL import Image
import os

def split_image(input_image, output_folder, cols=4, rows=4):
    """
    Split an image into equal square parts.
    
    Args:
        input_image: Path to the input image file
        output_folder: Path to the folder where split images will be saved
        cols: Number of columns (default: 4)
        rows: Number of rows (default: 4)
    """
    # Load the image
    img = Image.open(input_image)
    width, height = img.size
    
    # Calculate dimensions for each part
    cell_width = width // cols
    cell_height = height // rows
    
    # Use the larger dimension to make square (to avoid cutting off content)
    square_size = max(cell_width, cell_height)
    
    # Create output directory if it doesn't exist
    os.makedirs(output_folder, exist_ok=True)
    
    # Split the image into parts
    part_number = 1
    for row in range(rows):
        for col in range(cols):
            # Calculate cell boundaries
            cell_left = col * cell_width
            cell_top = row * cell_height
            cell_right = cell_left + cell_width
            cell_bottom = cell_top + cell_height
            
            # Expand horizontally to make it square (center the expansion)
            horizontal_excess = square_size - cell_width
            crop_left = max(0, cell_left - (horizontal_excess // 2))
            crop_right = min(width, crop_left + square_size)
            # Adjust left if right edge hits boundary
            if crop_right == width:
                crop_left = width - square_size
            
            crop_top = cell_top
            crop_bottom = cell_bottom
            
            # Crop the image to square
            part = img.crop((crop_left, crop_top, crop_right, crop_bottom))
            
            # Resize to ensure perfect square if needed
            if part.size != (square_size, square_size):
                part = part.resize((square_size, square_size), Image.Resampling.LANCZOS)
            
            # Convert to RGBA if not already (for transparency support)
            if part.mode != 'RGBA':
                part = part.convert('RGBA')
            
            # Get the pixel data
            data = part.getdata()
            
            # Create new image data with transparency
            # Assuming white background should be transparent
            new_data = []
            for item in data:
                # Change all white (or near-white) pixels to transparent
                if item[0] > 240 and item[1] > 240 and item[2] > 240:
                    new_data.append((255, 255, 255, 0))  # Transparent
                else:
                    new_data.append(item)
            
            # Update image with new data
            part.putdata(new_data)
            
            # Save the part
            filename = f'{output_folder}/part_{part_number:02d}.png'
            part.save(filename)
            print(f'Saved {filename} ({part.size[0]}x{part.size[1]})')
            
            part_number += 1
    
    print(f'\nSuccessfully split image into {part_number - 1} square parts!')
    print(f'Original size: {width}x{height}')
    print(f'Cell size: {cell_width}x{cell_height}')
    print(f'Square size: {square_size}x{square_size}')

# Example usage
if __name__ == '__main__':
    split_image('bh2.png', 'bh2', cols=4, rows=4)
```
This will create:
```
output_stickers/
 ├── sticker_1.png
 ├── sticker_2.png
 ├── ...
 └── sticker_16.png
```

### 📲 3. Import Stickers into Telegram

1. Open Telegram
2. Search for `@Stickers` (official Telegram Stickers bot)
3. Follow the steps to:
   - Create a new pack
   - Upload each PNG sticker
   - Add emoji associations
   - Publish your pack

📌 **Telegram Stickers Bot UI Placeholder**

`<ADD_TELEGRAM_SCREENSHOT_HERE>`

### 📱 4. Import Stickers into WhatsApp

WhatsApp now allows direct sticker creation.

**Steps:**

1. Open WhatsApp
2. Open any chat
3. Tap **Sticker → Create Sticker**
4. Upload your PNG sticker
5. Add text / crop / background removal
6. Save to your sticker pack

📌 **WhatsApp Sticker Creation UI Placeholder**

`<ADD_WHATSAPP_SCREENSHOT_HERE>`

