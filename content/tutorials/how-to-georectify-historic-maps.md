---
title: "How to Georectify Historic Maps Tutorial"
date: "2026-04-12"
publishDate: "2026-04-12"
author: "Katherine Kania"
weight: 3
aliases:
  - "/2026/04/12/how-to-georectify-historic-maps-tutorial/"
---

**How to Georectify Historic Maps in QGIS**

  1. **Load the Historic Map**
     * Open QGIS.
     * Go to **Raster** > **Georeferencer**.
     * Click **Open Raster** and select your scanned historic map.
  2. **Set the Coordinate Reference System (CRS)**
     * Click **Settings** > **Transformation Settings**.
     * Choose a CRS that matches your project (e.g., EPSG:4326 for WGS 84).
  3. **Add Control Points**
     * Click **Add Point** and select a known location on the historic map.
     * Enter the real-world coordinates (or click **From Map Canvas** to choose from an existing GIS layer).
     * Repeat for multiple control points across the map for accuracy.
  4. **Set Transformation Parameters**
     * Choose a transformation method (e.g., **Thin Plate Spline** or **Polynomial 2**).
     * Set resampling method (e.g., **Nearest Neighbor**).
     * Choose an output location for the rectified map.
  5. **Start Georeferencing**
     * Click **Start Georeferencing** to align the historic map with real-world coordinates.
     * Once completed, the georectified map will be added as a layer in QGIS.
  6. **Adjust and Validate**
     * Adjust transparency in **Layer Properties** > **Transparency** to compare with the basemap.
     * Check alignment with known features.

**How to Georectify Historic Maps Online**

**Map Warper**

Go to [mapwarper.net](http://mapwarper.net).

**Browse all maps**

1. To browse a map that has already been uploaded and georectified on
   [mapwarper.net](http://mapwarper.net), click the **Browse All Maps** tab at the top
   of the page.
2. In the search box, set your parameters – location and year.
3. Browse search results to find a georectified map.

**Upload a map**

1. To upload a map, sign up for a map warper account or log into your existing account.
2. Click the 'Upload Map' tab on the top menu bar.
   - Add your map's metadata to the form.
   - At the bottom of the page, either upload an image file or upload from a URL. Click 'Create.'
3. Your page will reload and your map file should appear on the next page. Navigate to
   the 'Rectify' tab.
   - Add control points. The uploaded map will appear on the left of a split screen with
     a world map on the right.
   - Zoom and scroll in on the world map and navigate as close as you can to the location
     of your historic map.
   - Identify a **specific location** (like a point of interest, street, building,
     landmark, etc) on your historic map, and the _same_ point on the world map. In the
     corner of the split screen is a **green location marker.** Click to activate. Then
     _on both maps_ click to mark the point of interest as close as you can to its exact
     location on both maps. When done click **'add control point'** on the bottom of the
     split screen. Continue this process at least 3 times (the more points you have, the
     more precise your georectification will be).
4. When done, click 'warp the image' and the historic map will be superimposed on the
   world map in the correct geographic location.

**Video Tutorial:** [youtu.be/EIZj67YgOXE](http://youtu.be/EIZj67YgOXE)
