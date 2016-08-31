See Checklist - TCS Login for information how to get to here...

# Move commands

There is one move command: `goto`.

The relevant part of TCA usage pattern:

```
  tca goto home [ce]
  tca goto home2 [cw]
  tca goto altaz <ALT> <AZ> [ce | cw | cc] [quick | track] [force]
  tca goto local <HA> <DE> [ce | cw | cc] [notrack [quick]] [force]
  tca goto celest <RA> <DE> [ce | cw | cc] [quick] [notrack] [force]
  tca goto internal <int_HA> <int_DE> [quick] [force]
  tca goto star <NAME> [ce | cw | cc] [quick] [notrack] [force]
  tca goto cat <NAME> [(from <CATALOG>)] [ce | cw | cc] [quick] [notrack] [force]
  tca goto this
```

### Special `goto` positions

There are two home positions: `home` and `home2`.

#### `home` - parking position with tube opening accessible from south balcony
#### `home2` - parking position with tube opening accessible from north balcony

There is also position `this` which is useful to start tracking on the coordinates where telescope is pointing currently.

It is possible to specify different chirality for both home positions.

### Coordinate systems

There are multiple systems of coordinates used, some are standard while other are specific to this telescope.

> TODO: describe all coordinate systems, link

Some of those coordinates can be used with `goto` command.

####  `celest`

This is the most commonly used coordinate system. It uniquely identifies any point in the sky, regardless of observer's location on Earth.

Chirality option is available when using these coordinates.

##### right ascension (`RA`)

Measured in  _hours_ (`1h = 15°`).

##### declination (`DE`)

Measured in degrees.


#### `local`

##### hour angle (`HA`)

This coordinate is dependent on observer's location, and on time of the day. `0h` is always at the meridian. 

This coordinate is measured in _hours_ (`1h = 15°`)

Chirality option is available when using these coordinates.

##### declination (`DE`)

`local` coordinate system has one coordinate, declination, in common with `celest` - their values are always the same.



#### `altaz`

Current position on the sky, as measured from horizon up (altitude) and from North to East (azimuth).

Note that in other software packages, the order of these coordinates is often reversed (AZ, then ALT).

Both coordinates are measured in degrees.

Chirality option is available when using these coordinates.

##### altitude (`ALT`)

#### azimuth (`AZ`)


#### `internal`

Coordinates that uniquely determinate position of the telescope. These coordinates do **not** match any real astronomical coordinates, and are only useful in context of this telescope.

Chirality option is not available when using these coordinates.


### Catalogue positions

#### `star`

A shortcut for 20 brightest stars, which can be specified by name.

example:

    d goto star Vega

#### `cat`

Catalogue entriers...

> TODO finish...
