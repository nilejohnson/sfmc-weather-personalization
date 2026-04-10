# Weather Personalization in SFMC

A working example of weather-based email personalization in Salesforce Marketing Cloud, using a free weather API (Open-Meteo) to drive dynamic content in emails.

One email template, 5 weather categories, and temperature-based sub-variations for some of them. Subscribers in different cities get completely different emails based on their local forecast.

## What's in this folder

- `script-activity.html` - the SSJS code for the Script Activity (runs in Automation Studio)
- `data-extensions.md` - setup guide for the 3 DEs you need
- `email-template.html` - the full email template with AMPscript
- `subject-line.txt` - dynamic subject line setup (paste into the email subject field)

## How it all fits together

1. **Script Activity** runs daily, calls Open-Meteo for each city, categorizes the weather (Sunny, Rainy, Cold, Snowy, Cloudy), and upserts a row into the `WeatherData` DE.
2. **Email template** pulls the subscriber's `CityID` from the sendable DE, looks up the matching row in `WeatherData`, and shows the right content block based on `WeatherCategory`.
3. **Subject line and preheader** are also set dynamically in the AMPscript header block.

## Setup order

1. Create the 3 Data Extensions (see `data-extensions.md`)
2. Test the script on a CloudPage first to make sure the API works and the DE gets populated
3. Once that works, drop the script into a Script Activity in Automation Studio
4. Schedule the automation to run daily (early morning, before your sends)
5. Build the email in Content Builder, paste the template HTML, and set the subject line
6. Preview with different test subscribers to see the weather variants

## Weather API

- Open-Meteo: https://open-meteo.com/
- Free, no API key needed
- Returns 7-day forecast with temperature, precipitation, and WMO weather codes

## Weather categories

The script looks at today's weather code and temperature, then assigns one of:

- **Snowy** - weather code 71-77
- **Rainy** - weather code 51-67 or 80-82
- **Sunny** - weather code 0-1 AND temp above 15C
- **Cold** - temp below 5C (and not snowy or rainy)
- **Cloudy** - everything else (default fallback)

Order matters: precipitation type takes priority over temperature, so someone in 3C rain gets the Rainy email, not the Cold one.

## Notes

- The script has hardcoded cities for this demo. In a real setup you'd pull them from the Cities DE or use subscriber-level location data.
- Temperature-based sub-personalization is built into the Sunny, Rainy, and Cold blocks (different products at different temp ranges).
- The `WeatherData` DE name is referenced in the SSJS by exact string, so don't rename it without updating the script.
