def process_html_and_download_images(html: str, story_id: str, section: str):

    if not html:
        return ""

    soup = BeautifulSoup(html, "html.parser")

    for tag in soup(["script", "style"]):
        tag.decompose()

    images = soup.find_all("img")

    img_folder = os.path.join("downloads", story_id, section)
    os.makedirs(img_folder, exist_ok=True)

    all_ocr_lines = []

    for idx, img in enumerate(images, start=1):
        src = img.get("src")
        if not src:
            continue

        save_path = os.path.join(img_folder, f"image_{idx}.png")
        downloaded_path = _download_ado_image(src, save_path)

        if downloaded_path:
            ocr_text = extract_text_from_image(downloaded_path)

            if ocr_text and len(ocr_text.strip()) > 10:
                cleaned = clean_ocr_text(ocr_text)

                if cleaned:
                    # Split into lines & remove obvious UI junk
                    for line in cleaned.split("\n"):
                        line = line.strip()

                        if not line:
                            continue

                        # Remove short noise lines
                        if len(line) < 3:
                            continue

                        all_ocr_lines.append(line)

    raw_text = soup.get_text(separator="\n")
    clean_text = _normalize_text(raw_text)

    # Remove duplicates between HTML and OCR
    html_lines = set(clean_text.split("\n"))

    unique_ocr_lines = [
        line for line in all_ocr_lines
        if line not in html_lines
    ]

    if unique_ocr_lines:
        combined_ocr = "\n".join(unique_ocr_lines)
        final_text = clean_text.strip() + "\n\n" + combined_ocr
    else:
        final_text = clean_text

    return final_text
