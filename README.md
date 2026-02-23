import re


def clean_ocr_text(text: str) -> str:
    if not text:
        return ""

    # Fix common OCR mistakes
    replacements = {
        "Ceypass": "Bypass",
        "Oi": "Of",
        "Olnpm": "HPML",
        "D allow": "Allow",
        "4 Electronic Deli": "Electronic Delivery",
        "¥": "",
        "|": "",
        "v]": "",
    }

    for wrong, correct in replacements.items():
        text = text.replace(wrong, correct)

    # Remove excessive spaces
    text = re.sub(r"[ \t]+", " ", text)

    # Remove duplicate blank lines
    text = re.sub(r"\n{2,}", "\n", text)
------------------------------------------------------------
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
            cleaned_ocr = clean_ocr_text(ocr_text)
            all_ocr_text.append(cleaned_ocr)

raw_text = soup.get_text(separator="\n")
clean_text = _normalize_text(raw_text)

# 🔥 Remove duplicate lines between HTML and OCR
if all_ocr_text:
    combined_ocr = "\n".join(all_ocr_text)

    # Remove OCR lines already present in HTML
    for line in combined_ocr.split("\n"):
        if line.strip() and line.strip() in clean_text:
            combined_ocr = combined_ocr.replace(line, "")

    final_text = clean_text + "\n\n" + combined_ocr.strip()
else:
    final_text = clean_text

return final_text
    return text.strip()
