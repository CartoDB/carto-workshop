# How to create a vintage Africa map

+ Based on [Mamata Akella](https://team.carto.com/u/mamataakella/me)'s CARTO Summit workshop inspired by this "Submarine Cable Map":

<br>
![submarine cable map](https://github.com/CartoDB/cdmx-training/blob/master/03-cartography/exercises/img/submarinecablemap.png)
<br>

## Resources

+ [.carto file](https://drive.google.com/file/d/0B9k_lcYQZACgY1k2RHV3MGx3MTg/view?usp=sharing)
+ image url: `https://s3.amazonaws.com/com.cartodb.users-assets.production/production/mamatablog/assets/20151025140245land_paper.png`

## Continent

+ Import carto file into your account.
+ Open the map
+ Rename your map as "Vintage Africa Map".
+ Order and rename your layers as follows:
  1. `ne_50m_admin_0_countries` > "Countries"
  2. `continent` > "Continent"
  3. `ne_50m_ocean` > "Ocean"
+ Select Africa:
  1. Click on *ADD ANALYSIS* just below "Continent" layer name
  2. Select *Filter by column value*
  3. Click on `ADD ANALYSIS`
  4. Set parameters as folows:
    * *COLUMN*: `continent`
    * *INPUT*: `Africa`
    * *RESULT*: `Show`
  5. Click on *APPLY*

<br>
![filter by column value](https://github.com/CartoDB/cdmx-training/blob/master/03-cartography/exercises/img/filterbycolval.png)
<br>

+ Create coastal ripple effect:
  1. Click on *+* symbol button to add a new analysis 
  2. Select *Create  Areas of influence*
  3. Click on *ADD ANALYSIS*
  4. Set parameters as folows:
    * *TYPE*: `Distance`
    * *UNITS*: `miles`
    * *RADIUS*: `100`
    * *TRACTS*: `6`
    * *BOUNDARIES*: `Intact`
  5. Click on `APPLY`

<br>
![ripples](https://github.com/CartoDB/cdmx-training/blob/master/03-cartography/exercises/img/ripples.png)
<br>

* Style:
  1. Go to *STYLE* tab
  2. Use the slider button to open CartoCSS view
  3. Apply this code:

  ```css
  #layer{
    line-width: ramp([data_range],0.4,1,equal);
    line-color: teal;
    line-opacity: 0.5;
  }
  ```

## Countries

### Global styles

+ In order to style all countries by categories and using a `pattern-file` follow these instructions:
  1. Go back to *LAYERS* pane
  2. Click on "Countries" layer
  3. Go to *STYLE* tab
  4. Open CartoCSS view by clickin the slider button
  5. Apply this code:

  ```css
  #layer{
    polygon-pattern-file: url(https://s3.amazonaws.com/com.cartodb.users-assets.production/production/mamatablog/assets/20151025140245land_paper.png);
    polygon-pattern-opacity: 0.3;
    polygon-fill: ramp([mapcolor7], cartocolor(Bold), category(7));
    polygon-opacity: 0.2;
  }
  ```

<br>
![global](https://github.com/CartoDB/cdmx-training/blob/master/03-cartography/exercises/img/global.png)
<br>

### Style for Africa

+ In order to highlight African countries add the following snippet at the end of the CartoCSS code. It will improve some cartographic-related issues, as line's color and offset, as well as labels.

```css
  [continent='Africa']{ 
    polygon-opacity: 0.3;
    line-width: 2;
    line-color: ramp([mapcolor7], cartocolor(Bold), category(7));
    line-opacity: 0.4;
    line-offset: -1;

    ::labels  {
    text-name: [abbrev];
    text-face-name: 'Gravitas One Regular';
    text-size: 10;
    text-fill: #000;
    text-label-position-tolerance: 0;
    text-halo-radius: 0;
    text-halo-fill: #6F808D;
    text-dy: 0;
    text-allow-overlap: true;
    text-placement: point;
    text-placement-type: dummy;
    text-transform: uppercase;
    text-character-spacing: 0.5;
    text-wrap-width: 25;
    }
  }
```

<br>
![africa](https://github.com/CartoDB/cdmx-training/blob/master/03-cartography/exercises/img/africa.png)
<br>

## Ocean

+ In order to style "Ocean" layer:
  1. Go back to *LAYERS* pane
  2. Click on "Ocean" layer
  3. Go to *STYLE* tab
  4. Open CartoCSS view by clickin the slider button
  5. Apply this code:

```css
#layer{
  polygon-fill: lighten(#b3d1cf,0);
  polygon-pattern-file: url(https://s3.amazonaws.com/com.cartodb.users-assets.production/production/mamatablog/assets/20151025140245land_paper.png);
  polygon-pattern-opacity: 0.4;
}
```

<br>
![ocean](https://github.com/CartoDB/cdmx-training/blob/master/03-cartography/exercises/img/ocean.png)
<br>

## Basemap

+ Finally, go back to *LAYERS* paen to style the basemap:
  1. Click on *Positron*.
  2. Set *COLOR* as `Source`
  3. Set `#c3d1c7` as *HEX* value.

<br>
![basemap](https://github.com/CartoDB/cdmx-training/blob/master/03-cartography/exercises/img/basemap.png)
<br>

## Publish

+ Go back to *LAYERS* pane in order to share your map:
  1. Click on *SHARE`
  2. Click on *PUBLISH* blue button
  3. Click on *PUBLISH* (below)
  3. Copy the link and paste it in a new browser tab: [https://team.carto.com/u/ramirocartodb/builder/9dc51d07-da6b-4694-a6ba-953cdaec9552/embed](https://team.carto.com/u/ramirocartodb/builder/9dc51d07-da6b-4694-a6ba-953cdaec9552/embed)

<br>
![map](https://github.com/CartoDB/cdmx-training/blob/master/03-cartography/exercises/img/map.png)
<br>

