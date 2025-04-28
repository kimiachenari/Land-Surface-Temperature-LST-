# 🌍 **Landsat 8: Land Surface Temperature (LST) Analysis** 🌍

Welcome to the **Land Surface Temperature (LST) Analysis** project using **Landsat 8** imagery! This project allows you to analyze the land surface temperature of **Mashhad** for the year **2021**. By processing **Landsat 8 (OLI/TIRS)** data, we calculate the **brightness temperature** (in Celsius) for every pixel, aggregate this data into yearly composites, and provide you with both visual and statistical insights into Tehran’s temperature dynamics.

With this tool, you can track how land temperatures fluctuate throughout the year, see temperature distribution across the region, and export the results in both tabular (CSV) and geospatial (GeoTIFF) formats for further analysis.

---

## 🧑‍🔬 **Key Features of This Project**

- **Brightness Temperature Calculation:** Convert Landsat 8’s thermal infrared data into temperature values (in Celsius).
- **LST Visualization:** Visualize the **Land Surface Temperature (LST)** across Tehran using a color palette to represent temperature variations.
- **Export Data:** Export LST data and statistics to CSV and GeoTIFF formats.
- **Temporal Analysis:** Calculate the average LST for the year 2021 and analyze temporal trends.

---

## 🏁 **Getting Started**

### 1. **Sign up for Google Earth Engine (GEE)**

First, you need a GEE account. If you don't have one, sign up here: [Earth Engine Signup](https://signup.earthengine.google.com/).

### 2. **Run the Script in GEE**

Once you're signed up, head over to the [Google Earth Engine Code Editor](https://code.earthengine.google.com/), paste in the provided code, and hit **Run**. The script will process the Landsat 8 imagery, calculate the land surface temperature, and output the results.

### 3. **Visualize the Results**

- After running the script, **LST** (Land Surface Temperature) will be visualized on the map.
- The temperature data will be aggregated to give you a **mean temperature** for the selected period (2021).
  
---

## 🔧 **How This Works**

### 1. **Load and Process Landsat 8 Data**

We start by loading the Landsat 8 **TOA (Top of Atmosphere)** data and filter it for **Tehran**. Specifically, we use the **Band 10 (Thermal Infrared Sensor)** data to extract the brightness temperature:

```javascript
var landsat8Toa = ee.ImageCollection('LANDSAT/LC08/C01/T1_TOA');
var temperature = landsat8Toa.filterBounds(geometry2)
    .select(['B10'], ['temp'])
    .map(function(image) {
      return image.subtract(273.15)  // Convert Kelvin to Celsius.
          .set('system:time_start', image.get('system:time_start'));
    });
```

### 2. **Clip the Temperature Data**

We clip the temperature data to the defined region of interest (Tehran) for better accuracy in further analysis:

```javascript
var clippedCol = temperature.map(function(im){ 
  return im.clip(geometry2);
});
```

### 3. **Generate the LST Composite for 2021**

Next, we aggregate the temperature data into a **mean composite** for the year **2021**:

```javascript
var LST = clippedCol.filterDate('2021-01-01', '2021-12-30')
    .reduce(ee.Reducer.mean())
    .select([0], ['winter']);
```

This composite provides the **mean land surface temperature (LST)** for each pixel over the entire year.

### 4. **LST Visualization**

The calculated LST composite is visualized using a color palette that represents temperature variations:

```javascript
Map.addLayer(LST, {min: -5, max: 45, palette: ['040274', '040281', '0502a3', '0502b8', '0502ce', '0502e6', '0602ff', '235cb1', '307ef3', '269db1', '30c8e2', '32d3ef', '3be285', '3ff38f', '86e26f', '3ae237', 'b5e22e', 'd6e21f', 'fff705', 'ffd611', 'ffb613', 'ff8b13', 'ff6e08', 'ff500d', 'ff0000', 'de0101', 'c21301', 'a71001', '911003']}, 'LST');
```

### 5. **Exporting Data**

The script exports the **mean LST data** as a **CSV file** for further analysis and a **GeoTIFF image** for spatial representation:

```javascript
Export.table.toDrive({
  collection: finalLST,
  description: 'LST_' + startdate + 'TO' + enddate,
  fileFormat: 'CSV'
});

Export.image.toDrive({
  image: LST,
  description: 'LST',
  region: geometry2,
  fileFormat: 'GeoTIFF',
});
```

This allows you to download the temperature data for offline analysis and use the GeoTIFF in GIS software.

### 6. **Mean LST Calculation**

The script also calculates the **mean LST value** for the entire region and prints the result:

```javascript
var mean = ee.Number(LST.reduceRegion({
  reducer: ee.Reducer.mean(),
  geometry: geometry2,
  scale: 30,
  maxPixels: 1e9
}).values().get(0));

print(mean, 'mean');
```

---

## 🗂 **Outputs**

- **LST Visualization**: A map showing the spatial distribution of land surface temperature across Tehran.
- **CSV Export**: A CSV file containing the **mean LST** for the region over the specified period (2021).
- **GeoTIFF Export**: A GeoTIFF image of the **LST composite** for use in GIS applications.
- **Mean LST**: The **average temperature** for the region, printed in the GEE console.

---

## 🚀 **Extend the Analysis**

- **Multi-Year Analysis**: Expand the analysis to include multiple years and explore seasonal changes.
- **Vegetation-Temperature Relationship**: Combine LST with vegetation indices (e.g., NDVI) to investigate the relationship between vegetation and land temperature.
- **Cloud-Free Compositing**: Enhance the analysis by using cloud-free composites to ensure better data quality.

---

## 📈 **Visualization Example**

Here's an example of how the temperature distribution is visualized on the map:

![LST Visualization](https://upload.wikimedia.org/wikipedia/commons/4/4c/Temperature_Map_Example.png)

---

