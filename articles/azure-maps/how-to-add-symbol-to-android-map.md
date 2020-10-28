---
title: Lägg till ett symbol lager till Android Maps | Microsoft Azure Maps
description: Lär dig hur du lägger till en markör till en karta. Se ett exempel som använder Azure Maps Android SDK för att lägga till ett symbol lager som innehåller punktbaserade data från en data källa.
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/26/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: acd5f06a5383308ce736f2860810ebee7e5bce28
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92897117"
---
# <a name="add-a-symbol-layer-to-a-map-using-azure-maps-android-sdk"></a>Lägg till ett symbol lager till en karta med Azure Maps Android SDK

Den här artikeln visar hur du återger punkt data från en data källa som ett symbol lager på en karta med hjälp av Azure Maps Android SDK.

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa stegen i den här artikeln fullständigt måste du installera [Azure Maps Android SDK](./how-to-use-android-map-control-library.md) för att läsa in en karta.

## <a name="add-a-symbol-layer"></a>Lägga till ett symbolskikt

Följ stegen nedan om du vill lägga till en markör på kartan med symbol skiktet:

1. Redigera **res**  >  **layout**  >  **activity_main.xml** så att det ser ut som i följande XML:
    
    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <FrameLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        >

        <com.microsoft.azure.maps.mapcontrol.MapControl
            android:id="@+id/mapcontrol"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            app:mapcontrol_centerLat="47.64"
            app:mapcontrol_centerLng="-122.33"
            app:mapcontrol_zoom="12"
            />

    </FrameLayout>
    ```

2. Kopiera följande kodfragment till **onCreate ()-** metoden för din `MainActivity.java` klass.

    ```Java
    mapControl.onReady(map -> {
    
        //Create a data source and add it to the map.
        DataSource dataSource = new DataSource();
        map.sources.add(dataSource);
    
        //Create a point feature and add it to the data source.
        dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));
    
        //Add a custom image icon to the map resources.
        map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
    
        //Create a symbol layer and add it to the map.
        map.layers.add(new SymbolLayer(dataSource,
            iconImage("my-icon")));
        });
    
    ```
    
    Kodfragmentet ovan hämtar först en Azure Maps kart kontroll instans med återanrops metoden **onReady ()** . Sedan skapas ett data käll objekt med hjälp av klassen **DataSource** och läggs till i kartan. Den lägger sedan till en **funktion** som innehåller en punkt geometri till den. En röd markör bild ställs sedan in som ikon för symbolen. Ett **symbol lager** använder text eller ikoner för att återge punktbaserade data figursatt i data källan som symbol på kartan. Ett symbol lager skapas sedan och data källan skickas till den för rendering och läggs sedan till i kartans lager.
    
    När du har lagt till kodfragmentet ovan `MainActivity.java` bör det se ut som det som visas nedan:
    
    ```Java
    package com.example.myapplication;
    
    import android.app.Activity;
    import android.os.Bundle;
    import com.mapbox.geojson.Feature;
    import com.mapbox.geojson.Point;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;
    import static com.microsoft.azure.maps.mapcontrol.options.SymbolLayerOptions.iconImage;
    public class MainActivity extends AppCompatActivity {
        
        static{
                AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
            }
    
        MapControl mapControl;
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapControl = findViewById(R.id.mapcontrol);
    
            mapControl.onCreate(savedInstanceState);
    
            mapControl.onReady(map -> {
    
                //Create a data source and add it to the map.
                DataSource dataSource = new DataSource();
                map.sources.add(dataSource);
            
                //Create a point feature and add it to the data source.
                dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));
            
                //Add a custom image icon to the map resources.
                map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
            
                //Create a symbol layer and add it to the map.
                map.layers.add(new SymbolLayer(dataSource,
                    iconImage("my-icon")));
            });
        }
    
        @Override
        public void onStart() {
            super.onStart();
            mapControl.onStart();
        }
    
        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
        }
    
        @Override
        public void onPause() {
            super.onPause();
            mapControl.onPause();
        }
    
        @Override
        public void onStop() {
            super.onStop();
            mapControl.onStop();
        }
    
        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapControl.onLowMemory();
        }
    
        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapControl.onDestroy();
        }
    
        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapControl.onSaveInstanceState(outState);
        }
    }
    ```
    
I det här läget bör du se en markör på kartan, som du ser här, om du kör programmet:

<center>

![PIN-kod för Android-karta](./media/how-to-add-symbol-to-android-map/android-map-pin.png)</center>

> [!TIP]
> Som standard optimerar symbol lager åter givningen av symboler genom att dölja symboler som överlappar varandra. När du zoomar in blir de dolda symbolerna synliga. Om du vill inaktivera den här funktionen och återge alla symboler hela tiden ställer du in `iconAllowOverlap` alternativet till `true` .

## <a name="next-steps"></a>Nästa steg

Information om hur du lägger till saker till din karta finns i:

> [!div class="nextstepaction"]
> [Lägga till former i en Android-karta](./how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Visa funktionsinformation](display-feature-information-android.md)