# Codes/Scripts/Resource link

## YouTube references:
- [Webflow Conf 2023 - Keynote](https://www.youtube.com/live/B08dZC4HruY?si=7WnJC4yH60BMCf7l)
- [Webflow Dropdown Automatically Opened On Page Load | Attribute Solution | UPDATE](https://m.youtube.com/watch?v=s7lIWdZpVZs&feature=youtu.be)
- [Apply This New Webflow Feature to All Images](https://www.youtube.com/watch?v=nbFzcgv58Zw&pp=ygUVYXNwZWN0IHJhdGlvIHdpemFyZHJ5)
- [Webflow's New Hidden Element](https://youtu.be/qULN82RqAHw?si=lleBeb_JAy5waPio)
- [Native SVGs in Webflow](https://www.youtube.com/watch?v=vaeN5MsA63s)


## DOM Element.json
```json
{
   "componentData":"{\"type\":\"@webflow/XscpData\",\"payload\":{\"nodes\":[{\"_id\":\"6e71c0c0-8e37-3003-1f90-63a35996dddf\",\"type\":\"DOM\",\"tag\":\"div\",\"classes\":[],\"children\":[],\"data\":{\"tag\":\"\",\"attributes\":[],\"editable\":true}}],\"styles\":[],\"assets\":[],\"ix1\":[],\"ix2\":{\"interactions\":[],\"events\":[],\"actionLists\":[]}},\"meta\":{\"unlinkedSymbolCount\":0,\"droppedLinks\":0,\"dynBindRemovedCount\":0,\"dynListBindRemovedCount\":0,\"paginationRemovedCount\":0}}",
   "componentFolder":"undefined",
   "componentImgURL":"https://uploads-ssl.webflow.com/631a1202a1fda92128f7e8ae/644a5ab7cbad85038fb71efe_miss-img.svg",
   "componentName":"DOM Element"
}
```

## /No need to import JQuery CDN on embed element
```js
var Webflow = Webflow || [];
Webflow.push(function() {
  /* Your Code Goes Here */
});

```

## Resetting interaction in Finsweet:
Reset IX
```js
/**reset IX**/
var Webflow = Webflow || [];
Webflow.push(function() {
    $('html').attr('fs-cmstabs-resetix', '64cb3cf628252df1fd7ef0fc');
    window.Webflow && window.Webflow.destroy();
    window.Webflow && window.Webflow.ready();
    window.Webflow && window.Webflow.require('ix2').init();
    document.dispatchEvent(new Event('readystatechange'));
});
/**close resetIX**/
```

## Tzeachten Mapbox Integration
```html
<link href="https://api.mapbox.com/mapbox-gl-js/v2.15.0/mapbox-gl.css" rel="stylesheet">
<script src="https://api.mapbox.com/mapbox-gl-js/v2.15.0/mapbox-gl.js"></script>
<script src="https://unpkg.com/@mapbox/mapbox-sdk/umd/mapbox-sdk.min.js"></script>
<script>
  var countryObject = {}; // my object
  var countryArray = []; // my array
  var restrictedAccess = {};
  var southAsia = {};
  var middleEast = {};
  var stores = {
    'type': 'FeatureCollection',
    'features': []
  };

  $('.country-list .country-item').each(function(index, value) {
    countryObject = {
      countryName: $(this).find('.country-name').text(),
      link: $(this).find('.country-name').attr('href')
    }
    if (countryObject.countryName !== 'Restricted Access' && countryObject.countryName !== 'South Asia' && countryObject.countryName !== 'Middle East')
      countryArray.push(countryObject);
    else
    if (countryObject.countryName == 'South Asia')
      southAsia = countryObject;
    if (countryObject.countryName == 'Middle East')
      middleEast = countryObject;
    if (countryObject.countryName == 'Restricted Access')
      restrictedAccess = countryObject;
  });
  $('.banner-close-wrapper').click(function() {
    sessionStorage.setItem("banner-hide", true);
  });
  $(function() {
    if (sessionStorage.getItem("banner-hide") === 'true') {
      $('.emergency-relief-wrapper').remove();
    }
    if ($('.emergency-relief-item').length === 0) {
      $('.emergency-relief-wrapper').remove();
    }
    var windowWidth = $(window).width();
    $(window).resize(function() {
      if ($(window).width() != windowWidth) {
        windowWidth = $(window).width();
        location.reload();
      }
    });
    mapboxgl.accessToken = 'pk.eyJ1IjoidHplYWNodGVuIiwiYSI6ImNsa3J5MGl1ZDA1d28zZG1vc3VndGNuajQifQ.hjKpt5hb39-OAmTThqM1Tg';
    const map = new mapboxgl.Map({
      container: 'map',
      style: 'mapbox://styles/tzeachten/clkrtshhy00av01px4ao4b362',
      center: [-121.9313426, 49.1164556],
      zoom: 12,
      zoomControl: false,
    });
    map.scrollZoom.disable();
    map.addControl(new mapboxgl.NavigationControl());
    map.resize();
    map.on('load', () => {
      map.addSource('places', {
        'type': 'geojson',
        'data': stores
      });
      buildLocationList(stores);
      addMarkers();
    });

    function addMarkers() {
      for (const marker of stores.features) {
        const el = document.createElement('div');
        el.id = `marker-${marker.properties.id}`;
        el.className = 'marker';
        new mapboxgl.Marker(el, {
            offset: [0, -23]
          })
          .setLngLat(marker.geometry.coordinates)
          .addTo(map);
        el.addEventListener('click', (e) => {
          flyToStore(marker);
          createPopUp(marker);
          const activeItem = document.getElementsByClassName('active');
          e.stopPropagation();
          if (activeItem[0]) {
            activeItem[0].classList.remove('active');
          }
          const listing = document.getElementById(
            `listing-${marker.properties.id}`
          );
          listing.classList.add('active');
        });
      }
    }

    function buildLocationList(stores) {
      for (const store of stores.features) {
        const listings = document.getElementById('listings');
        const listing = listings.appendChild(document.createElement('div'));
        listing.id = `listing-${store.properties.id}`;
        listing.className = 'item';
        const link = listing.appendChild(document.createElement('a'));
        link.href = '#';
        link.className = 'title';
        link.id = `link-${store.properties.id}`;
        link.innerHTML = `<div class="item-map-content"><div class="item-icon-wrapper"><div class="location-icon-content"> <img src="https://uploads-ssl.webflow.com/64c17b16cbd2fd1069061df0/64def8728abd255d07ffbb4d_map-location-vector.svg" alt="location-icon"></div></div><div class="title">${store.properties.name}</div></div>`;
        const locaddress = listing.appendChild(document.createElement('div'));
        locaddress.innerHTML = `<div class="icon-content-wrapper margin-bottom-8">
  <div class="icon-detail-wrapper">
    <div class="icon-details-wrapper">
      <img src="https://uploads-ssl.webflow.com/64c17b16cbd2fd1069061df0/64ded2203240d6e100cc8984_location-icon-vector.svg" alt="location-icon" class="icon-img">
    </div>
  </div>
  ${store.properties.address}
</div>`
        const details = listing.appendChild(document.createElement('div'));
        let showPhone = 'none';
        let showFormattedPhone = 'none';
        let showEmail = 'none';
        if (store.properties.phone !== '') {
          showPhone = 'flex';
        }
        if (store.properties.phoneFormatted !== '') {
          showFormattedPhone = 'flex';
        }
        if (store.properties.email !== '') {
          showEmail = 'flex';
        }

        details.innerHTML = `<div class="icon-flex-wrapper margin-bottom-8">
<div class="icon-content-wrapper" style="display: ` + showPhone + `">
 <div class="icon-detail-wrapper">
    <div class="icon-details-wrapper">
      <img src="https://uploads-ssl.webflow.com/64c17b16cbd2fd1069061df0/64ded406c57fbbefc30f28a4_phone-icon-vector.svg" alt="location-icon" class="icon-img">
    </div>
  </div>
  ${store.properties.phone}
</div>
<div class="phone-spacer" style="display: ` + showPhone + `"></div> <div class="icon-content-wrapper" style="display: ` + showFormattedPhone + `">
  <div class="icon-detail-wrapper">
    <div class="icon-details-wrapper">
      <img src="https://uploads-ssl.webflow.com/64c17b16cbd2fd1069061df0/64ded405284aac100e0d8f0f_business-icon-vector.svg" alt="location-icon" class="icon-img">
    </div>
  </div>
 ${store.properties.phoneFormatted}
</div>
</div>
<div class="icon-flex-wrapper">
<div class="icon-content-wrapper" style="display: ` + showEmail + `">
  <div class="icon-detail-wrapper">
    <div class="icon-details-wrapper">
      <img src="https://uploads-ssl.webflow.com/64c17b16cbd2fd1069061df0/64f67f1e32f87d8b035a1907_tzeachten-email-icon.svg" alt="location-icon" class="icon-img">
    </div>
  </div>
 ${store.properties.email}
</div>
</div>`
        listing.addEventListener('click', function() {
          for (const feature of stores.features) {
            if (this.id === `listing-${feature.properties.id}`) {
              flyToStore(feature);
              createPopUp(feature);
            }
          }

          var index = $('.item').index(this);
          $('.item').removeClass('active');
          $('.item').eq(index).addClass('active');

        });
      }
    }

    function flyToStore(currentFeature) {
      map.flyTo({
        center: currentFeature.geometry.coordinates,
        zoom: 15
      });
    }

    function createPopUp(currentFeature) {
      const popUps = document.getElementsByClassName('mapboxgl-popup');
      if (popUps[0]) popUps[0].remove();
      let showPhone = 'none';
      let showFormattedPhone = 'none';
      let showEmail = 'none';
      if (currentFeature.properties.phone !== '') {
        showPhone = 'flex';
      }
      if (currentFeature.properties.phoneFormatted !== '') {
        showFormattedPhone = 'flex';
      }
      if (currentFeature.properties.email !== '') {
        showEmail = 'flex';
      }
      const popup = new mapboxgl.Popup({
          closeOnClick: false
        })
        .setLngLat(currentFeature.geometry.coordinates)
        .setHTML(
          `<div class="item-map-content"><div class="item-icon-wrapper"><div class="location-icon-content"> <img src="https://uploads-ssl.webflow.com/64c17b16cbd2fd1069061df0/64def8728abd255d07ffbb4d_map-location-vector.svg" alt="location-icon"></div></div><div class="title">${currentFeature.properties.name}</div></div>
            
            <div class="icon-content-wrapper margin-bottom-8">
  <div class="icon-detail-wrapper">
    <div class="icon-details-wrapper">
      <img src="https://uploads-ssl.webflow.com/64c17b16cbd2fd1069061df0/64ded2203240d6e100cc8984_location-icon-vector.svg" alt="location-icon" class="icon-img">
    </div>
  </div>
  ${currentFeature.properties.address}
</div>
<div class="icon-flex-wrapper margin-bottom-8""><div class="icon-content-wrapper" style="display: ` + showPhone + `">
  <div class="icon-detail-wrapper">
    <div class="icon-details-wrapper">
      <img src="https://uploads-ssl.webflow.com/64c17b16cbd2fd1069061df0/64ded406c57fbbefc30f28a4_phone-icon-vector.svg" alt="location-icon" class="icon-img">
    </div>
  </div>
  ${currentFeature.properties.phone}
</div>
<div class="phone-spacer" style="display: ` + showPhone + `"></div> <div class="icon-content-wrapper" style="display: ` + showFormattedPhone + `">
  <div class="icon-detail-wrapper">
    <div class="icon-details-wrapper">
      <img src="https://uploads-ssl.webflow.com/64c17b16cbd2fd1069061df0/64ded405284aac100e0d8f0f_business-icon-vector.svg" alt="location-icon" class="icon-img">
    </div>
  </div>
 ${currentFeature.properties.phoneFormatted}
</div>
</div>
<div class="icon-flex-wrapper">
<div class="icon-content-wrapper" style="display: ` + showEmail + `">
  <div class="icon-detail-wrapper">
    <div class="icon-details-wrapper">
      <img src="https://uploads-ssl.webflow.com/64c17b16cbd2fd1069061df0/64f67f1e32f87d8b035a1907_tzeachten-email-icon.svg" alt="location-icon" class="icon-img">
    </div>
  </div>
 ${currentFeature.properties.email}
</div>
</div>`
        )
        .addTo(map);
    }
  });

</script>
```

## No-Scroll Code
Here is the CSS:
```html
<style> .no-scroll {overflow:hidden;} </style>
```
Here is the jQuery:
```html
<script>
  $('.menu').on('click', function() {
    $('body').addClass('no-scroll');
  });
  $('.close').on('click', function() {
    $('body').removeClass('no-scroll');
  });
</script>
```

## Await for elements before running a function
```js
        const startObservingElements = ({
          selectors,
          callback
        }) => {
          const observer = new MutationObserver((_mutations, obs) => {
            let foundSelectors = [];

            selectors.forEach((selector) => {
              // Use jQuery to select the element
              const element = $(selector);
              if (element.length > 0 && !foundSelectors.includes(selector)) {
                // Element exists and is not already in the found list, mark as found
                foundSelectors.push(selector);

                // Check if all selectors have been found
                if (foundSelectors.length === selectors.length) {
                  // All elements are found, run the callback
                  callback();

                  // Disconnect the observer as its job is done
                  obs.disconnect();
                }
              }
            });
          });

          observer.observe($('body')[0], {
            childList: true,
            subtree: true,
          });
        };

        const runFn = () => {
                /* Your function logic here */
        }

        startObservingElements({
          selectors: [
            /* Your selector here */
          ],
          callback: runFn
        });

```
