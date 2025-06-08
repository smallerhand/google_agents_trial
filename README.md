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

## Running with Docker

You can also build and run this application using Docker. This simplifies dependency management and provides a consistent environment.

**Prerequisites for Docker:**
- Docker installed on your system.
- You still need to have your Google Cloud Project ID and Location correctly set in `app.py` **before building the Docker image**, or you'll need to adapt the Docker setup to pass these as environment variables at runtime. For this guide, we assume you've updated `app.py`.
- You will need to provide Google Cloud credentials to the container.

**1. Build the Docker Image:**
   Navigate to the project's root directory (where the `Dockerfile` is located) and run:
   ```bash
   docker build -t image-describer .
   ```

**2. Run the Docker Container:**
   You need to make your Google Cloud credentials available to the application inside the container.

   *   **Using Application Default Credentials (ADC) from your host:**
       If you have run `gcloud auth application-default login` on your host machine, you can mount the gcloud ADC directory into the container. The location of ADC varies by OS:
        *   Linux/macOS: `~/.config/gcloud`
        *   Windows: `%APPDATA%\gcloud`

       To run the container (example for Linux/macOS):
       ```bash
       docker run -p 5000:5000 \
           -v ~/.config/gcloud:/root/.config/gcloud:ro \
           -e GOOGLE_APPLICATION_CREDENTIALS=/root/.config/gcloud/application_default_credentials.json \
           image-describer
       ```
       For Windows (using PowerShell and assuming ADC are in the default location):
       ```powershell
       docker run -p 5000:5000 `
           -v "$env:APPDATA\gcloud:/root/.config/gcloud:ro" `
           -e GOOGLE_APPLICATION_CREDENTIALS="/root/.config/gcloud/application_default_credentials.json" `
           image-describer
       ```
       *(Note: The target path inside the container for gcloud config for ADC to be picked up by Python client libraries is typically `/root/.config/gcloud` when the container runs as root, which is common for default Docker setups. If your base image or user setup differs, this path might need adjustment.)*

   *   **Using a Service Account Key File:**
       If you have a service account JSON key file:
       1.  Place the key file in a secure location on your host machine (e.g., `/path/to/your/keyfile.json`).
       2.  Run the container, mounting the key file and setting the `GOOGLE_APPLICATION_CREDENTIALS` environment variable:
           ```bash
           docker run -p 5000:5000 \
               -v /path/to/your/keyfile.json:/app/keyfile.json:ro \
               -e GOOGLE_APPLICATION_CREDENTIALS=/app/keyfile.json \
               image-describer
           ```
           *(Ensure the path `/app/keyfile.json` inside the container is accessible by the application. You might need to adjust permissions or the path if necessary. Using `:ro` makes the mounted file read-only in the container, which is good practice for credentials.)*

**Important Considerations for Docker:**
-   **Project ID & Location in `app.py`**: Remember that the `PROJECT_ID` and `LOCATION` in `app.py` are baked into the image at build time with the current `Dockerfile`. If you need to change these frequently without rebuilding, you would need to modify `app.py` to read them from environment variables and then pass those environment variables with `docker run -e ...`.
-   **Accessing the Application**: Once the container is running, open your browser and go to `http://localhost:5000` (or `http://<docker-machine-ip>:5000` if using Docker Machine).

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
