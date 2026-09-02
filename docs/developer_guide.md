# OUTDATED - ON TO-DO LIST TO UPDATE

### Developer Guide

This guide is for developers who want to contribute to or understand how this project works. Feel free to improve this guide at any time.

### Purpose of this project:

This project is built upon the famous youtube-dl project. With proper use of
multithreading and the use of Libcurl as a download engine, it can reach
up to 10x higher speeds in cases involving HLS or fragmented video files.
In addition, it can download general files as well.

The GUI is based on tkinter, which is lightweight and responsive.

This project was never made to compete with other download managers. It
is simply a "hopefully useful," simple enough, and fast video downloader.

---

### Current project logic:

Generally, FireDM uses Libcurl as a download engine through threads to
achieve multiple connections. For videos, youtube-dl is used to extract
video information from a specific URL. Its sole role is to extract video
information; it has no other duties for youtube-dl.

FFMPEG will be used for post-processing, e.g. muxing audio and video, merging
HLS video segments into one video file, and performing other useful media
manipulation.

The current application design adopts the "MVC" design pattern, where the
"Model" is in model.py, the controller is in controller.py, and the view is
tkview.py for the tkinter GUI or cmdview.py, which runs interactively in the
terminal.

An "observer" pattern is also used to notify the controller when the model
"data object" changes its state.

Workflow using the GUI is as follows:

* The user enters a URL in the URL entry widget.
* The GUI will ask the controller to process the URL.
* The controller will make a data object, e.g. ObservableDownloadItem(), and call its
  url_update method, which sends an HTTP request to the remote server. Based on the
  received "response headers" from the server, it will update properties such as the
  name, size, MIME type, download URL, etc., and the controller will be notified of changes.
* In case the MIME type is HTML, the controller will pass the URL to youtube-dl to search for
  videos, and it will create an ObservableVideo() object.
* The controller will send update messages to the GUI, and it will show file information in the main tab.
* When the user presses the download button, the GUI will ask the controller to download the current file.
  The controller will make some pre-download checks and, if everything is OK, it will create a thread
  to run the "brain function" to download the file.
* The brain function will run both the "thread manager" and file manager.
* The thread manager will make multiple connections to download the file in "chunks."
* The file manager will write completed chunks into the target file.
* After completing all chunks, post-processing will be run if necessary, e.g. FFmpeg
  will mux audio and video into one final video file.

---

### Files:

* **FireDM.py:** Main file. It will start the application in either
  interactive terminal mode or GUI mode.

* **config.py:** Contains all shared variables and settings.

* **utils.py:** Contains all helper functions.

* **tkview.py:** This module contains the application GUI, designed using tkinter.

* **settings.py:** This is where settings and the download items list are saved and loaded.

* **brain.py:** Every download item object will be sent to brain to
  download it. This module contains the thread manager and file manager.

* **cmdview.py:** An interactive user interface that runs in the terminal.

* **controller.py:** A part of the "MVC" design that contains the
  application logic and communicates with both the Model and View.

* **model.py:** Contains "ObservableDownloadItem" and "ObservableVideo", which act
  as the Model in the "MVC" and observer designs.

* **downloaditem.py:** Contains the DownloadItem class, which contains
  information about a download item. There are many DownloadItem objects in
  this project, often referred to simply as "d" or "self.d".

* **video.py:** Contains the Video class, which is subclassed from
  DownloadItem for video objects. This file also contains most video-related
  functions, e.g. merge_video_audio, pre_process_hls, etc.

* **worker.py:** The Worker class acts as a standalone worker. Each
  worker is responsible for downloading a chunk or file segment.

* **update.py:** Contains functions for updating the FireDM frozen version
  (currently the cx_Freeze Windows portable version), as well as updating
  youtube-dl.

* **version.py:** Contains the version number, which is date-based. Example
  content: **version** = '2020.8.13'

* **dependency.py:** Contains a list of required external packages for
  FireDM to run and has an "install_missing_pkgs" function to install
  missing packages automatically.

* **ChangeLog.txt:** Logs changes for each new version.

---

### Documentation format:

Code documentation, if found, does not follow a specific format.
This is something that should be fixed. The selected project format should
follow the Google Python Style Guide. Resources:

* [Example Google Style Python Docstrings](https://sphinxcontrib-napoleon.readthedocs.io/en/latest/example_google.html#example-google)
* ["Google Python Style Guide"](http://google.github.io/styleguide/pyguide.html)

---

### How can I contribute to this project:

* Check open issues in this project and find something that you can fix.
* It's recommended that you open an issue first to discuss what you want
  to do. This will create better communication with other developers
  working on the project.
* Submit a pull request and add a good description of your modification.
* It doesn't matter how small the change you make is; it can still make a
  difference.
