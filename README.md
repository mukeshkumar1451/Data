def process_html_and_download_images(html: str, story_id: str, section: str):

    if not html:
        return "", ""

    soup = BeautifulSoup(html, "html.parser")

    for tag in soup(["script", "style"]):
        tag.decompose()

    images = soup.find_all("img")

    img_folder = os.path.join("downloads", story_id, section)
    os.makedirs(img_folder, exist_ok=True)

    all_ocr_text = []

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

    combined_ocr = "\n\n".join(all_ocr_text)

    return clean_text, combined_ocr
