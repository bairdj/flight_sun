# Flight Sun

Flight Sun is a Python package designed to calculate the proportion of a flight
that will be in sunlight.

The sunrise and sunset times of the origin and destination airports are easily obtainable,
so for short flights, it can usually be assumed whether a flight is a day or night flight.

However, for long flights, especially those travelling longitudinally, this calculation can
be more difficult. For example, a 17-hour flight from London to Perth, Australia can take off
and land in daylight, but spend the majority of the flight in darkness.

This package breaks a flight path into segments of day and night, allowing the proportion of
a flight that is in sunlight to be calculated.

## Features

- GeoJSON rendering of flight path with feature properties for whether the segment is in sunlight
- Integrated IATA and ICAO airport lookups (using airportsdata package). This retrieves the coordinates and local time
zones of airports.

## Assumptions

- The flight path is a direct great circle route between the origin and destination airports
- The aircraft flies at a constant speed
- Flight altitude is not considered

## Installation

Flight Sun can be installed using pip:

    pip install flight_sun

## Usage
    from flight_sun.flight import Flight
    from flight_sun.visualisation import generate_geojson
    from datetime import datetime
    import json
    
    take_off_time = datetime.fromisoformat("2023-02-11 11:50:00")
    landing_time = datetime.fromisoformat("2023-02-12 12:25:00")
    flight = Flight.from_iata_codes("LHR", "PER", take_off_time, landing_time)
    
    # Get sunlight proportion
    print(f"Sunlight proportion: {flight.proportion_in_sunlight():.2%}")
    
    # Get sunlight segments
    print("Sunlight segments:")
    for segment in flight.light_segments:
        print(f"Sunlight: {segment.sun_up}, start: {segment.start}, end: {segment.end}")
    
    # Render to GeoJSON
    geojson = generate_geojson(flight.light_segments)
    with open("flight.geojson", "w") as f:
        json.dump(geojson, f)

### Example output
    
    Sunlight proportion: 45.23%
    Sunlight segments:
    Sunlight: True, start: 2023-02-11 11:50:00+00:00, end: 2023-02-11 14:58:00+00:00
    Sunlight: False, start: 2023-02-11 14:58:00+00:00, end: 2023-02-12 00:03:00+00:00
    Sunlight: True, start: 2023-02-12 00:03:00+00:00, end: 2023-02-12 04:25:00+00:00

## Future ideas

- Add support for more complex flight paths (e.g. via waypoints)
- Use real-world flight data to create a better time-distance interpolation function
- Consider the position of the sun in relation to the aircraft i.e. which side of the aircraft is in sunlight