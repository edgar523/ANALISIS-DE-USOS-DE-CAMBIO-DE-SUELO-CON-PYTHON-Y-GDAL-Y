# ANALISIS-DE-USOS-DE-CAMBIO-DE-SUELO-CON-PYTHON-Y-GDAL-Y
                                      # AUTORES: Edgar Rosales, Oscar campos , Ángel Galván
                      Facultad de Ingenieria Civil, PE-ITG, Coquimatlán, Jardines del Llano, C.P. 28400, Col.
                                  edgargabriel_rosales@ucol.mx, Ocampos@ucol.mx, anroga.017@gmail.com.`
                      
### RESUMEN

El siguiente programa estará hecho con la finali-dad de calcular el cambio de la vegetación en el suelo de un archivo raster, donde se mostrará las zonas donde han ocurrido más cambios, el pro-grama no será realizado en un ambiente grafico de Qgis o ArcGis, será realizado en un IDE de Python el cual nosotros utilizaremos spyder de Anaconda navigator, el programa tendrá mucha utilidad para a la toma de decisiones sobre en qué zonas está siendo más afectada  por estos cambios y las consecuencias que podrá traer este.


PALABRA CLAVE: CALCULAR EL CAMBIo DE VGETACION

**Abstract:

The following program will be made with the pur-pose of calculating the change of the vegetation in the soil of a raster file, where the areas where more changes have occurred will be shown, the program will not be carried out in a graphic envi-ronment of Qgis or ArcGis, will be carried out in a Python IDE which we will use Anaconda naviga-tor spyder, the program will be very useful for making decisions on which areas are most af-fected by these changes and the consequences that this may bring.

Keywords:   Program to calculate the change of vegetation.

### 1.introduccion

El programa está hecho con la finalidad de mos-trarnos en que zonas de la mancha urbana de Coquimatlán se han mostrado cambio de uso de suelo en el índice de vegetación, ¿Cómo se rea-lizara esto?, para poder realizar este descarga-remos información de las páginas de gobierno que manejan información espacial, como lo son el ENIGI, CONAFOR, CONAGUA y SAGARPA que son algunas de las instituciones que mane-jan información que manejan este tipo de infor-mación que necesitaremos para que el programa pueda procesar.
Para poder realizar este análisis de cambio será necesario realizar comparativas de imágenes raster de diferentes años, en esta ocasión se realizará el análisis desde 2014 hasta el 2019, descargaremos imagen raster de cada uno de estos años y las iremos exportando a nuestro programa, que funciona con la lectura de bandas y columnas de una imagen a otra y las almacena para poder comparar con la siguiente imagen del año siguiente. 
Los datos que se van utilizar en este programa son imagen rastré de radiación de ca-lor(temperaturas), imágenes de la mancha urbana sin ningún tipo de filtro e imágenes en infrarrojo.

### 2.desarrollo
Primeramente vamos a declarar nuestras librerías: 

`from osgeo import ogr, gdal, osr`

`import os`

`import numpy as np`

`import matplotlib.pyplot as plt`

Para que este programa funcione de la mejor manera, necesitamos una imagen NDVi del 2014 y otro del 2019, para poder ver el cambio de uso de suelo entre esos años. 
El NDVI es la diferencia entre el infrarrojo más la rojo.
Image-2019
`path_B5_2019="../Image20190203clip/LC08_L1TP_029047_20190203_20190206_01_T1_B5_clip.TIF`

`path_B4_2019="../Image20190203clip/LC08_L1TP_029047_20190203_20190206_01_T1_B4_clip.TIF`

Image-2014
`path_B5_2014="../Image20140205clip/LC08_L1TP_029047_20140205_20170307_01_T1_B5_clip.TIF`

`path_B4_2014="../Image20140205clip/LC08_L1TP_029047_20140205_20170307_01_T1_B4_clip.TIF`


Archivos de salida
Posteriormente vamos a definir los archivos de salida, porque lo vamos a gravar en Output donde vamos a crear en NBI del 2014 y 2019, y también el cambio del NBI al igual que los con-tornos,  donde evidenciaremos que zonas han cambiado más y que zonas han cambiado menos.

`path_NDVI_2019 = '../Output/NDVI2019.tif`

`path_NDVI_2014 = '../Output/NDVI2014.tif`

`path_NDVIChange_19_14 = '../Output/NDVIChange_19_14.tif`


Contornos
`contours_NDVIChange_19_14 = '../Output/NDVIChange_19_14.shp`
En la siguiente línea de código vamos a abrir todos nos NDVI.

abrir banda raster

`B5_2019 = gdal.Open(path_B5_2019)`

`B4_2019 = gdal.Open(path_B4_2019)`

`B5_2014 = gdal.Open(path_B5_2014)`

`B4_2014 = gdal.Open(path_B4_2014)`


Leer bandas como matrices array

`B52019_Data = B5_2019.GetRasterBand(1).ReadAsArray().astype(np.float32)`

`B42019_Data = B4_2019.GetRasterBand(1).ReadAsArray().astype(np.float32)`

`B52014_Data = B5_2014.GetRasterBand(1).ReadAsArray().astype(np.float32)`

`B42014_Data = B4_2014.GetRasterBand(1).ReadAsArray().astype(np.float32)`

Cuando vamos a hacer un geoproceso tenemos que asegurarnos de varias cosas: que todos tengan el mismo tamaño, que todos tengan los mismos parámetros de geotransformación.
Posteriormente vamos a comparar si tienen el mismo sistema de proyección, el mismo tama-ño, y si su parámetro de geotransformación sean lo mismo.

Después vamos a calcular el NDVI.

`print(B5_2014.GetProjection()[:80])`

 `print(B5_2019.GetProjection()[:80])`
 
 `if B5_2014.GetProjection()[:80]==B5_2019.GetProjection()[:80]: print('SRC OK') 
     print(B52014_Data.shape)
     print(B52019_Data.shape)`

  `if B52014_Data.shape==B52019_Data.shape: print('Array Size OK')
    print(B5_2014.GetGeoTransform())
    print(B5_2019.GetGeoTransform())`

 `if B5_2014.GetGeoTransform()==B5_2019.GetGeoTransform(): print('Geotransformation OK')`
 
 `geotransform = B5_2014.GetGeoTransform()`
 
    originX,pixelWidth,empty,finalY,empty2,pixelHeight=geotransform
    cols =  B5_2014.RasterXSize
    rows =  B5_2014.RasterYSize
    projection = B5_2014.GetProjection()
    finalX = originX + pixelWidth * cols
    originY = finalY + pixelHeight * rows
    ndvi2014 = np.divide(B52014_Data - B42014_Data, B52014_Data+ B42014_Data,where=(B52014_Data - B42014_Data)!=0)
    ndvi2014[ndvi2014 == 0] = -999

    ndvi2019 = np.divide(B52019_Data - B42019_Data, B52019_Data+ B42019_Data,where=(B52019_Data - B42019_Data)!=0)
     ndvi2019[ndvi2019 == 0] = -999 `

`Después vamos a crear una opción para que grave el RASTER, con una opción que convierte en TIF.

`def saveRaster(dataset,datasetPath,cols,rows,projection):`

    rasterSet = gdal.GetDriverByName('GTiff').Create(datasetPath, cols, rows,1,gdal.GDT_Float32)
    rasterSet.SetProjection(projection)
    rasterSet.SetGeoTransform(geotransform)
    rasterSet.GetRasterBand(1).WriteArray(dataset)
    rasterSet.GetRasterBand(1).SetNoDataValue(-999)
    rasterSet = None `
    
  
Posteriormente vamos a crear una función que plotiee NDVI’S
Y como resultado nos muestra en NDVI del 2014 y 2015.

`saveRaster(ndvi2014,path_NDVI_2014,cols,rows,projection)`

`saveRaster(ndvi2019,path_NDVI_2019,cols,rows,projection)` 

`extentArray = [originX,finalX,originY,finalY]`

`def plotNDVI(ndviImage,extentArray,vmin,cmap):`

      ndvi = gdal.Open(ndviImage)
      ds2019 = ndvi.ReadAsArray()
      plt.figure(figsize=(20,15))
     im = plt.imshow(ds2019, vmin=vmin, cmap=cmap, extent=extentArray)#
      plt.colorbar(im, fraction=0.015)
      plt.xlabel('Este')
      plt.ylabel('Norte')
      plt.show()
      
`plotNDVI(path_NDVI_2014,extentArray,0,'YlGn')`

[https://drive.google.com/drive/u/0/folders/1lRnTgzln2YNbscz9WoY4Zqif_5CnwZmo]











