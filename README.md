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
-------------------------------------------------------------------------
def process_html_and_download_images(html: str, story_id: str, section: str) -> str:

    if not html:
        return ""

    soup = BeautifulSoup(html, "html.parser")

    for tag in soup(["script", "style"]):
        tag.decompose()

    images = soup.find_all("img")

    img_folder = os.path.join("downloads", story_id, section)
    os.makedirs(img_folder, exist_ok=True)

    all_ocr_text = []

    # -------------------------------------------------
    # DOWNLOAD + OCR (RAW ONLY)
    # -------------------------------------------------
    for idx, img in enumerate(images, start=1):
        src = img.get("src")
        if not src:
            continue

        save_path = os.path.join(img_folder, f"image_{idx}.png")
        downloaded_path = _download_ado_image(src, save_path)

        if downloaded_path:
            ocr_text = extract_text_from_image(downloaded_path)

            if ocr_text and len(ocr_text.strip()) > 10:
                all_ocr_text.append(ocr_text.strip())

    raw_text = soup.get_text(separator="\n")
    clean_text = _normalize_text(raw_text)

    # -------------------------------------------------
    # MERGE OCR NATURALLY (NO DEBUG MARKERS)
    # -------------------------------------------------
    if all_ocr_text:
        combined_ocr = "\n\n".join(all_ocr_text)
        final_text = clean_text + "\n\n" + combined_ocr
    else:
        final_text = clean_text

    return final_text
