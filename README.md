<!---
{
  "id": "05894bad-a8de-413a-86ce-166579682bbc",
  "depends_on": [
    "bae22056-8b3c-4c71-807d-68abc7171b36", 
    "8acee03e-6020-4e3b-86b7-d7747b216258", 
    "b668e389-98db-4777-bc4c-190de535836c", 
    "e8add8e9-7a67-4b50-af89-6c1ce6558e0d"
    ],
  "author": "Stephan Bökelmann",
  "first_used": "2025-06-02",
  "keywords": ["curl", "file download", "images", "batch download", "HTTP"]
}
--->

# Advanced File Download with `curl`

> In this exercise you will learn how to download files from the internet using `curl`. Furthermore, you will explore downloading multiple files, naming output files, and using options that are helpful when collecting assets such as images for LaTeX projects.

### Introduction

While `curl` is often used for API calls, one of its original and most common uses remains downloading files directly from the web. Since `curl` operates as a pure client that understands HTTP(S), it can request static files like images, PDFs, and archives just as easily as structured API responses.

When you run a basic `curl` command such as:

```bash
curl https://example.com/file.png
```

it streams the response to your terminal's standard output. If the response is binary (like an image), you will see gibberish printed to your terminal. To actually save the file, you need to redirect the output.

With the `-o` option, you specify the output filename directly:

```bash
curl -o output.png https://example.com/file.png
```

This will download `file.png` and save it as `output.png`. You can also use `-O` (capital letter O) to automatically save the file using its remote name:

```bash
curl -O https://example.com/file.png
```

When downloading multiple files, `curl` can be called repeatedly, or you can loop over multiple URLs in a script to download batches of resources. This is particularly useful when preparing directories of assets for LaTeX documents (e.g. inserting figures as we saw in previous exercises).

### Further Readings and Other Sources

* [curl manpage](https://curl.se/docs/manpage.html)
* [curl - The Art of Scripting HTTP Requests](https://everything.curl.dev/)
* [YouTube - Download Files with curl](https://www.youtube.com/watch?v=bj4M5jz1k1g)

---

## Tasks

### Task 0: Verify curl installation

Make sure `curl` is installed:

```bash
curl --version
```

If not installed, install it (example for Debian-based systems):

```bash
sudo apt update && sudo apt install curl
```

### Task 1: Download a single file and save it

Create a new project directory:

```bash
mkdir -p download-practice/figures
cd download-practice/figures
```

Now download a picture and save it with a chosen name:

```bash
curl -o file.png https://github.com/STEMgraph/05894bad-a8de-413a-86ce-166579682bbc/blob/master/assets/RUB-Studio_Ghibli.png?raw=true
```

### Task 2: Use `-O` to preserve remote filenames

Sometimes the remote filename is acceptable. In that case:

```bash
curl -O https://github.com/STEMgraph/05894bad-a8de-413a-86ce-166579682bbc/blob/master/assets/RUB-Vincent_van_Gogh.png?raw=true
```

You should now have both `file.png` and `RUB-Vincent_van_Gogh.png` in the `figures/` directory.

### Task 3: Download multiple files using a list

Create a file `urls.txt` in your project directory containing:

```text
https://github.com/STEMgraph/05894bad-a8de-413a-86ce-166579682bbc/blob/master/assets/RUB-Fan_Kuan.png?raw=true
https://github.com/STEMgraph/05894bad-a8de-413a-86ce-166579682bbc/blob/master/assets/RUB-Tim_Burton.png?raw=true
```

Use a loop to download both files automatically:

```bash
while read url; do curl -O "$url"; done < urls.txt
```

Alternatively, with `xargs`:

```bash
cat urls.txt | xargs -n 1 curl -O
```

### Task 4: Use progress bars and resume interrupted downloads

To see progress while downloading:

```bash
curl -O -# https://github.com/STEMgraph/05894bad-a8de-413a-86ce-166579682bbc/blob/master/assets/RUB-Stanley_Kubrick.png?raw=true
```

If a download was interrupted, you can resume it:

```bash
curl -O -C - https://github.com/STEMgraph/05894bad-a8de-413a-86ce-166579682bbc/blob/master/assets/RUB-Stanley_Kubrick.png?raw=true
```

### Task 5: Query a GitHub API and download filtered files

You can also query public APIs to generate download lists automatically. For example, you can list files from a public GitHub repository using their REST API:

```bash
curl -s https://api.github.com/repos/STEMgraph/05894bad-a8de-413a-86ce-166579682bbc/contents/assets | jq -r '.[].download_url'
```

To filter all files starting with `RUB` and ending with `.png`, use `grep`:

```bash
curl -s https://api.github.com/repos/STEMgraph/05894bad-a8de-413a-86ce-166579682bbc/contents/assets \
  | jq -r '.[].download_url' \
  | grep '/RUB.*\.png$'
```

Finally, download all matching files automatically:

```bash
curl -s https://api.github.com/repos/STEMgraph/05894bad-a8de-413a-86ce-166579682bbc/contents/assets \
  | jq -r '.[].download_url' \
  | grep '/RUB.*\.png$' \
  | xargs -n 1 curl -O
```

This pattern allows you to dynamically pull files based on file name patterns directly from repository listings.

---

## Questions

1. What is the difference between `-o` and `-O` in `curl`?
2. Why is it dangerous to print binary data to standard output?
3. What happens if you run the download command again for the same file?
4. How can you automate downloading multiple files?
5. Why might you want to resume an interrupted download with `-C -`?
6. Why might querying an API for filenames be useful before downloading files?

---

## Advice

Downloading files with `curl` is extremely common in both scripting and manual workflows. Mastering how to save files properly and automate batch downloads will save you time when preparing resources for documentation, including LaTeX projects. Always verify you are downloading the correct file types, and organize files into meaningful directories from the start (e.g. `figures/`, `data/`, `pdfs/`). As your projects grow, these habits will make your work cleaner and more maintainable. Learning how to combine `curl`, `jq`, `grep`, and `xargs` allows you to automate very complex download scenarios efficiently.
