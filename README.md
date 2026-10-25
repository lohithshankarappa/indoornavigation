# Indoor Navigator

A small-area indoor navigation prototype based on the provided project outline:

- **OpenCV ORB** matches a camera view against self-collected landmark images.
- **Ultralytics YOLO** optionally detects supported objects in the frame. Detection is an alert only; it does not establish location or decide that a path is blocked.
- **Dijkstra's algorithm** selects a least-cost route through the mapped walkways.
- **Streamlit** provides a browser interface for camera snapshots, uploaded images, route selection, and map display.

## Run it

Use Python 3.10 or newer. From this folder, create and activate a virtual environment, then install dependencies:

```bash
python -m venv .venv
# Windows PowerShell
.venv\Scripts\Activate.ps1
pip install -r requirements.txt
streamlit run app.py
```

The first YOLO run may download `yolov8n.pt`; disable YOLO if you only want to try routing/localization. The camera view uses the browser's camera picker and may ask for camera permission.

## Add landmark photos

Capture recognizable corridor, entrance, or junction views from several angles and under different lighting. Place JPG/PNG photos in `data/landmarks/`; name each file with its corresponding map node id, such as `lobby.jpg` or `junction_2.png`. Multiple photos can share a node id prefix only if you update `localization.py` to aggregate reference views; the starter currently uses one file per id.

The matcher accepts a landmark when at least 12 distinctive ORB keypoint matches pass its ratio test. This is a prototype threshold, not a calibrated position-confidence estimate.

## Edit the map

Edit `data/map.json`. A node has a unique `id`, human-readable `name`, and map coordinates `x`/`y`. An edge joins two nodes and has a positive `cost` (for example, distance, travel time, or a manually assigned penalty). Edges are treated as bidirectional. If you want one-way corridors, change the graph builder in `indoor_nav/routing.py`.

The included map is fictional sample data. Replace it with a measured map before demonstrating navigation in a real building. Dijkstra optimizes the costs in this file and does not account for obstacles detected in the image.

## Folders

- `app.py` — Streamlit user interface
- `indoor_nav/routing.py` — map validation, Dijkstra, next-step direction
- `indoor_nav/localization.py` — ORB landmark matching
- `indoor_nav/detection.py` — optional detector helper
- `data/map.json` — editable example graph
- `data/landmarks/` — add your reference images here

## Current limits

This demonstration localizes from a single still frame, not continuous tracking. It does not estimate orientation precisely, dynamically avoid obstacles, or provide accessibility/evacuation guarantees. A real deployment needs a surveyed map, on-site landmark data, camera/device testing, and human validation.
