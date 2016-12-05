# How to create a vintage Africa map

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
  1. Click on `ADD ANALYSIS` just below "Continent" layer name
  2. Select "Filter by column value"
  3. Click on `ADD ANALYSIS`
  4. Set parameters as folows:
    * COLUMN: `continent`
    * INPUT: `Africa`
    * RESULT: `Show`
  5. Click on `APPLY`

<br>
![filter by column value](https://github.com/CartoDB/cdmx-training/blob/master/03-cartography/exercises/img/filterbycolval.png)
<br>

+ Create coastal ripple effect:
  1. Click on "+" to add a new analysis 
  2. Select "Create  Areas of influence"
  3. Click on `ADD ANALYSIS`
  4. Set parameters as folows:
    * TYPE: `Distance`
    * UNITS: `miles`
    * RADIUS: `100`
    * TRACTS: `6`
    * BOUNDARIES: `Intact`
  5. Click on `APPLY`

<br>
![ripples](https://github.com/CartoDB/cdmx-training/blob/master/03-cartography/exercises/img/ripples.png)
<br>

* Style:
  1. Go to STYLE tab
  2. Click on `CartoCSS`
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

+ In order to style all countries follow these instructions:
  1. Go back to the main menu
  2. Click on "Countries" layer
  3. Go to STYLE tab
  4. Click on `CartoCSS`
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

+ Ramp line color
+ Line offset
+ Labels

```css
#layer{
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
}
``` 

## Ocean

`@ocean: #c3d1c7;`


```css
  polygon-fill: lighten(#b3d1cf,0);
  polygon-pattern-file: url(https://s3.amazonaws.com/com.cartodb.users-assets.production/production/mamatablog/assets/20151025140245land_paper.png);
  polygon-pattern-opacity: 0.4;
```
