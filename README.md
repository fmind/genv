# GenV: Generative AI for Video Analytics 📹🤖

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1TgiWawhrLRVH6UB2I0zbCylrYnu5gO3Y)

This repository contains a Google Colaboratory notebook designed to automatically analyze video recordings (specifically tailored for Google Meet recordings) using Generative AI (Vertex AI Gemini models) and extract structured insights.

## Motivation

Manually reviewing meeting recordings to extract key information like decisions, action items, and summaries is time-consuming. This notebook aims to automate this process by leveraging the power of multimodal generative AI models. By providing a video file, it generates a structured summary including:

* Meeting Title
* Concise Summary
* Questions & Answers
* Unanswered Questions
* Projects Discussed
* Action Items (with owners and deadlines, if mentioned)
* Decisions & Suggestions
* Technical Insights
* Other Key Topics

This saves significant time and makes meeting outcomes more accessible and actionable.

## How it Works (Briefly)

1.  **Setup & Configuration:** Connects to Google Cloud Platform (GCP) and Google Drive, requiring user configuration for Project ID, GCS Bucket, and the path to video recordings.
2.  **Video Discovery:** Scans the specified Google Drive path for video files modified within a configurable timeframe (`SINCE_DAYS`).
3.  **Cloud Storage Upload:** Uploads newly found video files to a specified Google Cloud Storage (GCS) bucket for processing via the Vertex AI API. (Skips existing files).
4.  **Vertex AI Analysis:** For each video, it sends the GCS URI to the configured Gemini model via the Vertex AI API.
5.  **Structured Prompting:** Uses a predefined prompt and a Pydantic `MeetingInsight` schema to instruct the model on *what* information to extract and *how* to structure it (JSON format).
6.  **Output Generation:** Parses the structured JSON response from the AI model.
7.  **Export:** Saves the extracted insights in two formats:
    * `video_insights.jsonlines`: Each line contains the raw JSON output for one video.
    * `video_insights.md`: A human-readable Markdown file compiling the insights from all processed videos.
8.  **Download (Optional):** Automatically downloads the generated `.jsonlines` and `.md` files.

## Features

* Automated analysis of video recordings (defaults to Google Meet `.mp4` files).
* Leverages Google's Vertex AI Gemini models for multimodal understanding.
* Uses Pydantic schemas for reliable, structured JSON output.
* Extracts a comprehensive set of insights (summary, Q&A, actions, decisions, etc.).
* Filters videos based on recent modification date.
* Configurable AI model, temperature, GCP settings, and video paths.
* Outputs results in both machine-readable (`.jsonlines`) and human-readable (`.md`) formats.
* Designed to run easily within Google Colaboratory.

## Setup

Before running the notebook, ensure you have the following prerequisites configured:

1.  **Google Cloud Platform (GCP):**
    * **Project:** Have a GCP project created or select an existing one.
    * **Enable API:** Enable the **Vertex AI API** within your project.
    * **Project ID Secret:** In Google Colab, go to the "Secrets" tab (🔑 icon on the left) and add a secret named `GENV_PROJECT_ID` with your GCP Project ID as the value. Alternatively, you can hardcode it in the `PROJECT_ID` variable within the notebook (not recommended for sharing).
2.  **Google Cloud Storage (GCS):**
    * **Bucket:** Create a GCS bucket in your project. This bucket is used as temporary storage for the videos during API calls.
    * **Update Variable:** Set the `BUCKET` variable in the notebook's "CONFIGS" section to the name of your GCS bucket.
3.  **Google Drive:**
    * **Authorize:** You will be prompted to authorize Colab to access your Google Drive when running the notebook.
    * **Video Location:** Ensure your video files (e.g., Google Meet recordings) are located in the path specified by the `VIDEOS_PATH` variable in the "CONFIGS" section. The default is `/content/drive/MyDrive/Meet Recordings`.

## Usage

1.  Complete the **Setup** steps above.
2.  Open the `GenV_Generative_AI_for_Video_Analytics.ipynb` notebook in Google Colab (click the badge above).
3.  Review and adjust parameters in the **CONFIGS** section as needed (e.g., `SINCE_DAYS`, `VIDEOS_PATH`, `MODEL`).
4.  Run all cells in the notebook (`Runtime` -> `Run all`).
5.  Authorize Google Drive access when prompted.
6.  The notebook will output progress information, including which files are uploaded and analyzed.
7.  Extracted insights will be printed and optionally downloaded as `video_insights.jsonlines` and `video_insights.md`.

## Configuration Parameters

You can customize the notebook's behavior via the variables in the **CONFIGS** section:

* `MODEL`: The Vertex AI Gemini model to use (e.g., `gemini-1.5-flash-latest`, `gemini-1.0-pro-vision`). *Note: Model availability and capabilities may vary.*
* `TEMPERATURE`: Controls the creativity/randomness of the AI's response (0.0 = deterministic).
* `PROJECT_ID`: Your GCP Project ID (ideally set via Colab secrets).
* `LOCATION`: The GCP region for Vertex AI processing (e.g., `us-central1`).
* `BUCKET`: The name of your GCS bucket for temporary video storage.
* `SINCE_DAYS`: How many days back to look for new video files based on modification time.
* `VIDEOS_PATH`: The path within your Google Drive where the video files are stored.
* `DOWNLOAD`: Set to `True` to automatically download the output files, `False` otherwise.

## Adapting and Extending

This notebook is provided as a starting point. You are encouraged to copy, adapt, and extend it for your own needs:

* **Modify the `MeetingInsight` Schema:** Change the Pydantic models in the "ANALYSIS > Data Classes" section to extract different types of information or structure it differently. Remember to update the `ANALYSIS_TEMPLATE` prompt accordingly if you make significant changes.
* **Refine the Prompt:** Experiment with the `ANALYSIS_TEMPLATE` to guide the AI towards more specific or nuanced insights.
* **Support Other Video Sources:** Adapt the file discovery logic (`CONTENTS` section) to read videos from different locations.
* **Different Output Formats:** Modify the `EXPORTS` section to save insights in other formats (e.g., CSV, database).
* **Error Handling:** Improve error handling for API calls or file operations.
* **Cost Management:** Be mindful of GCP costs associated with Vertex AI API usage and GCS storage. Add cost estimation or tracking if needed.

## Disclaimer

* Generative AI models can sometimes produce inaccurate or unexpected results ("hallucinations"). Always review the generated insights for accuracy.
* The quality of the analysis heavily depends on the audio and video quality of the recordings.
* Ensure you have the necessary permissions and comply with privacy regulations when processing video recordings containing personal data.
* Using this notebook incurs costs on Google Cloud Platform (Vertex AI, Cloud Storage). Monitor your usage.

## License

This project is open-sourced under the [MIT License](LICENSE). (You would need to add a LICENSE file with the MIT license text to your repo).
