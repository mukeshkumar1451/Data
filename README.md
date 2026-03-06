from PIL import Image
import os

MAX_WIDTH = 1200
JPEG_QUALITY = 85


def resize_image_if_needed(image_path: str):

    try:

        with Image.open(image_path) as img:

            width, height = img.size

            if width <= MAX_WIDTH:
                return image_path

            ratio = MAX_WIDTH / float(width)
            new_height = int(height * ratio)

            resized = img.resize(
                (MAX_WIDTH, new_height),
                Image.LANCZOS
            )

            base, _ = os.path.splitext(image_path)

            resized_path = base + "_resized.jpg"

            resized.convert("RGB").save(
                resized_path,
                "JPEG",
                quality=JPEG_QUALITY,
                optimize=True
            )

            return resized_path

    except Exception:
        return image_path
