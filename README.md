# Country-wide Netherlands

Using [pyesridump](https://github.com/openaddresses/pyesridump), run:

```bash
esri2geojson https://basisregistraties.arcgisonline.nl/arcgis/rest/services/BAG/BAGv3/MapServer/0 netherlands_addresses.geojson
```

This source is ~9.7m records, so if it times out, pick back up where it left off by finding the last object returned:

```bash
tail -1 netherlands_addresses.geojson | jq '.properties.objectid'
```

Then run `esri2geojson` to pick back up where it left off: 

```bash
esri2geojson -p "WHERE=objectid > <# from above>" https://basisregistraties.arcgisonline.nl/arcgis/rest/services/BAG/BAGv3/MapServer/0 netherlands_addresses.geojson.2
```

If multiple attempts are needed to download all the data, then combine them into a single GeoJSON `FeatureCollection`.  

I then removed the properties not in the `addresses` layer conform by running this `jq` command:

```bash
jq -c '{type: .type, geometry: .geometry, properties: {adresseerbaarobjectid: .properties.adresseerbaarobjectid, huisnummer: .properties.huisnummer, huisletter: .properties.huisletter, huisnummertoevoeging: .properties.huisnummertoevoeging, openbareruimtenaam: .properties.openbareruimtenaam, postcode: .properties.postcode, woonplaatsnaam: .properties.woonplaatsnaam}}' netherlands_addresses.geojson > netherlands_addresses.geojson.final
```

Then zipped with:

```bash
zip nl-countrywide.addresses.geojson.zip netherlands_addresses.geojson.final
```

