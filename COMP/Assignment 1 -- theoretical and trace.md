sources:
- https://en.wikipedia.org/wiki/Geographic_coordinate_conversion#:~:text=sexagesimal%20degree:%20degrees%2C%20minutes%2C,positive%20and%20negative%20values%20properly
- https://share.google/4NIqymnyr1eAzr9RZ
- https://www.calgarytransit.com/fares---passes.html -- fare = $3.80
  

## Program in psudocode:
  
`START PROGRAM
  // --- Initialization of Constants ---
    // Destination coordinates for MRU's Homage statue in decimal degrees
    CONST MRU_HOMAGE_LATITUDE_DEC = 51.01376194
    CONST MRU_HOMAGE_LONGITUDE_DEC = 114.13369889

    // Earth's mean radius in kilometers
    CONST EARTH_RADIUS_KM = 6378

    // Base adult transit fare in dollars
    CONST BASE_TRANSIT_FARE = 3.80

    // --- Program Start and User Input ---
    DISPLAY "Welcome to the fare reduction calculator!"

    DISPLAY "Enter your starting latitude:"
    PROMPT_FOR_INPUT "Enter degrees (e.g., 51): ", user_lat_deg
    PROMPT_FOR_INPUT "Enter minutes (e.g., 2): ", user_lat_min
    PROMPT_FOR_INPUT "Enter seconds (precise to 3 decimal places, e.g., 40.445): ", user_lat_sec

    DISPLAY "Enter your starting longitude:"
    PROMPT_FOR_INPUT "Enter degrees (e.g., 114): ", user_lon_deg
    PROMPT_FOR_INPUT "Enter minutes (e.g., 2): ", user_lon_min
    PROMPT_FOR_INPUT "Enter seconds (precise to 3 decimal places, e.g., 39.106): ", user_lon_sec


    // Convert user's latitude to decimal degrees
    SET user_latitude_decimal = ABS(user_lat_deg) + (user_lat_min / 60) + (user_lat_sec / 3600)
    // Note: The problem states to enter positive numbers, so ABS() is for robustness.

    // Convert user's longitude to decimal degrees
    SET user_longitude_decimal = ABS(user_lon_deg) + (user_lon_min / 60) + (user_lon_sec / 3600)

    // --- Distance Calculation (Haversine Formula) ---

    // Calculate the change in latitude and longitude
    SET delta_lat = user_lat_rad - mru_lat_rad
    SET delta_lon = user_lon_rad - mru_lon_rad

    // Haversine formula - Step 1: Calculate intermediate value 'a'
	// Assuming that the trig functions use degrees as input units
    // a = sin²(Δφ/2) + cos(φA) * cos(φB) * sin²(Δλ/2)
    SET a_intermediate = (SINE(delta_lat / 2))^2 + COSINE(mru_lat_rad) * COSINE(user_lat_rad) * (SINE(delta_lon / 2))^2

    // Haversine formula - Step 2: Calculate intermediate value 'c'
    // c = 2 * atan2( √(a), √(1−a) )
    SET c_intermediate = 2 * ARCTAN2(SQUARE_ROOT(a_intermediate), SQUARE_ROOT(1 - a_intermediate))

    // Haversine formula - Step 3: Calculate the final distance 'd'
    // d = R * c
    SET unrounded_distance_km = EARTH_RADIUS_KM * c_intermediate

    // --- Fare Calculation ---
    // Calculate the fare reduction based on the unrounded distance
    // Formula: reduction = 1 / (0.4 + e^(3 - unrounded distance in km))
    SET fare_reduction_amount = 1 / (0.4 + EXPONENTIAL(3 - unrounded_distance_km))

    // Calculate the final fare before rounding
    SET final_fare_unrounded = BASE_TRANSIT_FARE - fare_reduction_amount

    // --- Round Final Answers ---
    // Round the trip distance to the nearest tenth of a km (1 decimal place)
    SET distance_km_rounded = ROUND(unrounded_distance_km, 1)

    // Round the fare to the nearest dime (2 decimal places)
    SET final_fare_rounded = ROUND(final_fare_unrounded, 2)

    // --- Display Final Output ---
    DISPLAY "Travelling from user's starting location (at " + user_lat_deg + "° " + user_lat_min + "' " + user_lat_sec + "\" North, " + user_lon_deg + "° " + user_lon_min + "' " + user_lon_sec + "\" West) to the Homage statue on MRU campus,"
    DISPLAY "the distance is approximately " + distance_km_rounded + " km."
    DISPLAY "Their fare would be reduced to $" + final_fare_rounded + "."
`END PROGRAM`

## Trace Table
Amusing user inputs 51° 2' 40.445" North, 114° 2' 39.106" West

| Step Number | **Description**                                     | **Variable name**        | **Values, calculations, and displayed results**                                                                                |
| :---------- | :-------------------------------------------------- | :----------------------- | :----------------------------------------------------------------------------------------------------------------------------- |
| 1           | Get user input for starting latitude degrees        | `user_lat_deg`           | 51                                                                                                                             |
| 2           | Get user input for starting latitude minutes        | `user_lat_min`           | 2                                                                                                                              |
| 3           | Get user input for starting latitude seconds        | `user_lat_sec`           | 40.445                                                                                                                         |
| 4           | Get user input for starting longitude degrees       | `user_lon_deg`           | 114                                                                                                                            |
| 5           | Get user input for starting longitude minutes       | `user_lon_min`           | 2                                                                                                                              |
| 6           | Get user input for starting longitude seconds       | `user_lon_sec`           | 39.106                                                                                                                         |
| 7           | Convert user's latitude to decimal degrees          | `user_latitude_decimal`  | 51 + (2 / 60) + (40.445 / 3600) = 51.044568                                                                                    |
| 8           | Convert user's longitude to decimal degrees         | `user_longitude_decimal` | 114 + (2 / 60) + (39.106 / 3600) = 114.044196                                                                                  |
| 9           | Haversine formula: Calculate intermediate value 'a' | `a_intermediate`         | (SINE(0.000537 / 2))^2 + COSINE(0.890326) * COSINE(0.890863) * (SINE(-0.001564 / 2))^2 = 0.000000314                           |
| 10          | Haversine formula: Calculate intermediate value 'c' | `c_intermediate`         | 2 * ARCTAN2(SQUARE_ROOT(0.000000314), SQUARE_ROOT(1 - 0.000000314)) = 0.001121                                                 |
| 11          | Haversine formula: Calculate the final distance 'd' | `unrounded_distance_km`  | 6378 * 0.001121 = 7.152                                                                                                        |
| 12          | Calculate the fare reduction amount                 | `fare_reduction_amount`  | 1 / (0.4 + EXPONENTIAL(3 - 7.152)) = 2.405                                                                                     |
| 13          | Calculate the final fare before rounding            | `final_fare_unrounded`   | 3.80 - 2.405 = 1.395                                                                                                           |
| 14          | Round the trip distance to one decimal place        | `distance_km_rounded`    | ROUND(7.152, 1) = 7.2                                                                                                          |
| 15          | Round the final fare to two decimal places          | `final_fare_rounded`     | ROUND(1.395, 2) = 1.40                                                                                                         |
| 16          | Display the first line of the final output          | Output                   | "Travelling from user's starting location (at 51° 2' 40.445" North, 114° 2' 39.106" West) to the Homage statue on MRU campus," |
| 17          | Display the second line of the final output         | Output                   | "the distance is approximately 7.2 km."                                                                                        |
| 18          | Display the third line of the final output          | Output                   | "Their fare would be reduced to $1.40."                                                                                        |
