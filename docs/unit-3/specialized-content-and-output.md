# Specialized Content & Outputs

- Response system primarily used for HTML
- Can also generate other content types like CSV, PDF, JSON, etc.
- Manipulate the `content_type` of HTTP responses to serve different MIME types.

## The Basics: Views and MIME-types {#the-basics-views-and-mime-types}

Every `HttpResponse` in Django carries a MIME type (Multipurpose Internet Mail Extensions), which tells the browser what kind of data is being sent.

* Default: `text/html; charset=utf-8`

* Manual Override: You can change this using the content_type argument.

```python
return HttpResponse("This is a plain text response.", content_type="text/plain")
```

**How Browsers Interpret Types**

The browser uses the MIME type (and the Content-Disposition header) to decide whether to:

1. Render the content (HTML, Images, PDFs in some browsers).

2. Download the content (CSV, ZIP, EXE).

3. Display as Code (JSON, XML).

## Producing CSV Output {#producing-csv}

- built-in `csv` module to generate CSVs on the fly
- no need of a template - write directly to the response object.

**Example: Exporting Users to CSV**

```python
import csv
from django.http import HttpResponse
from .models import Song

def export_songs_csv(request):

    # Create the HttpResponse object with the appropriate CSV header.
    response = HttpResponse(content_type='text/csv')
    response['Content-Disposition'] = 'attachment; filename="songs.csv"'

    writer = csv.writer(response)
    writer.writerow(['Song Title', 'Released Date', 'Author'])  # Header row

    for song in Song.objects.all():
        writer.writerow([song.title, song.released_date, song.author])

    return response
```

## Generating PDFs {#generating-pdfs}

- Generating PDF documents dynamically using libraries.
- Overview of libraries like `ReportLab` and `WeasyPrint`.

**Example: Basic PDF View (ReportLab)**
```python
from django.http import HttpResponse
from reportlab.pdfgen import canvas

def generate_pdf(request):
    # Create the HttpResponse object with the appropriate PDF headers.
    response = HttpResponse(content_type='application/pdf')
    response['Content-Disposition'] = 'attachment; filename="song_detail.pdf"'

    # Create the PDF object, using the response object as its "file."
    p = canvas.Canvas(response)

    # Draw things on the PDF. Here's where the PDF generation happens.
    p.drawString(100, 750, "Hello, this is a PDF document.")

    # Close the PDF object cleanly.
    p.showPage()
    p.save()

    return response
```

## Other Possibilities {#other-possibilities}

**Generating JSON (APIs)**

- Use `JsonResponse` from `django.http` for easy JSON responses.
- Example:
```python
    from django.http import JsonResponse

    def song_list_api(request):
        songs = list(Song.objects.values('id', 'title', 'author'))
        return JsonResponse({'songs': songs}) # Sets content_type to application/json
```

**Dynamic ZIP Files**

- Use `zipfile` module to create ZIP files on the fly.
- Example:
```python
    import zipfile
    from django.http import HttpResponse

    def download_zip(request):
        response = HttpResponse(content_type='application/zip')
        response['Content-Disposition'] = 'attachment; filename="songs.zip"'

        with zipfile.ZipFile(response, 'w') as zip_file:
            for song in Song.objects.all():
                zip_file.writestr(f"{song.title}.txt", f"Title: {song.title}, Author: {song.author}")

        return response
```

**Dynamic Images**

* Creating dynamic images using libraries like `Pillow(PIL)`


??? tip "Mini-Challenge: Add QR code in the song detail page that encodes the song's URL."

    Add a QR code generator or barcode generator to your project for dynamic image generation. Libraries like `qrcode` or `python-barcode` can help with this.