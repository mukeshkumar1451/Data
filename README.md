import logging
from PIL import Image

logger = logging.getLogger(__name__)

MAX_WIDTH = 1200
JPEG_QUALITY = 85


def resize_image_if_needed(image_path: str) -> str:
    try:
        with Image.open(image_path) as img:
            width, height = img.size

            if width <= MAX_WIDTH:
                return image_path

            ratio = MAX_WIDTH / float(width)
            new_height = int(height * ratio)

            resized_img = img.resize((MAX_WIDTH, new_height), Image.LANCZOS)

            resized_path = image_path.replace(".png", "_resized.jpg")

            resized_img.convert("RGB").save(
                resized_path,
                "JPEG",
                quality=JPEG_QUALITY,
                optimize=True
            )

            logger.info(f"🖼 Resized: {image_path} → {resized_path}")

            return resized_path

    except Exception as e:
        logger.error(f"❌ Image resize failed: {e}")
        return image_path
