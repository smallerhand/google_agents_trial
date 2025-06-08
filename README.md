# Image Description Web Application

This is a simple Flask web application that takes an image as input and uses the Vertex AI Imagen API to generate a description of the image.

## Features

- Upload an image (PNG, JPG, JPEG, GIF).
- View the uploaded image.
- Get a textual description of the image content using Google Cloud's Imagen model.

## Prerequisites

- Python 3.8+
- Google Cloud SDK installed and authenticated.
- A Google Cloud Project with the Vertex AI API enabled.
- Service account credentials for authentication (recommended for non-local development).

## Setup Instructions

1.  **Clone the repository:**
    ```bash
    git clone <repository-url>
    cd <repository-directory>
    ```

2.  **Create and activate a virtual environment:**
    ```bash
    python3 -m venv venv
    source venv/bin/activate  # On Windows use `venv\Scripts\activate`
    ```

3.  **Install dependencies:**
    ```bash
    pip install -r requirements.txt
    ```

4.  **Configure Google Cloud Authentication:**

    *   **Set your Project ID and Location:**
        Open `app.py` and replace the placeholder values for `PROJECT_ID` and `LOCATION` with your actual Google Cloud Project ID and the region where you want to run Vertex AI predictions (e.g., `us-central1`).

        ```python
        # In app.py
        PROJECT_ID = "your-actual-gcp-project-id"  # <--- REPLACE THIS
        LOCATION = "your-actual-gcp-region"    # <--- REPLACE THIS (e.g., us-central1)
        ```

    *   **Set up Application Default Credentials (ADC):**
        For local development, the easiest way to authenticate is by using the Google Cloud CLI:
        ```bash
        gcloud auth application-default login
        ```
        This command will open a browser window for you to log in with your Google account that has access to the configured project and Vertex AI API.

    *   **(Alternative) Using a Service Account Key:**
        For environments where you cannot use ADC directly (like some CI/CD systems or specific server setups), you can use a service account key.
        1.  Create a service account in your GCP project with the "Vertex AI User" role (or more restrictive permissions as needed).
        2.  Download the JSON key file for this service account.
        3.  Set the `GOOGLE_APPLICATION_CREDENTIALS` environment variable to the path of this JSON key file:
            ```bash
            export GOOGLE_APPLICATION_CREDENTIALS="/path/to/your/keyfile.json"
            ```
            On Windows:
            ```powershell
            $env:GOOGLE_APPLICATION_CREDENTIALS="C:\path\to\your\keyfile.json"
            ```
        **Important:** Do not commit your service account key file to the repository. Add it to your `.gitignore` file.

5.  **Ensure Vertex AI API is Enabled:**
    Make sure the Vertex AI API is enabled for your Google Cloud Project. You can do this through the Google Cloud Console.

## Running the Application

1.  **Start the Flask development server:**
    ```bash
    python app.py
    ```

2.  Open your web browser and navigate to `http://127.0.0.1:5000/`.

3.  Upload an image and see the description!

## Project Structure

-   `app.py`: The main Flask application file containing the routes and logic for image processing and API calls.
-   `requirements.txt`: Lists the Python dependencies.
-   `templates/`: Contains HTML templates.
    -   `index.html`: The main page for image upload and displaying results.
-   `static/`: Contains static files (CSS, JavaScript, images).
    -   `uploads/`: Directory where uploaded images are temporarily stored.
    -   `style.css`: (To be created) For basic application styling.
-   `README.md`: This file.

## Notes

- The `imagetext@0.0.1` model is used for captioning. Ensure this model or a compatible one is available in your specified GCP region.
- Error handling is implemented for common issues, but you might encounter others depending on your environment and GCP setup. Check the console output from `app.py` for detailed error messages.
