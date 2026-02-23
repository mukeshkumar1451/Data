def extract_text_from_image(image_path: str) -> str:
    try:
        if not os.path.exists(image_path):
            print(f"OCR DEBUG: File not found -> {image_path}")
            return ""

        image = cv2.imread(image_path)

        if image is None:
            print(f"OCR DEBUG: cv2 could not read image -> {image_path}")
            return ""

        gray = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
        gray = cv2.threshold(gray, 150, 255, cv2.THRESH_BINARY)[1]

        text = pytesseract.image_to_string(gray)

        print(f"OCR DEBUG: Extracted text length = {len(text)}")

        return text.strip()

    except Exception as e:
        print(f"OCR ERROR: {e}")
        return ""
