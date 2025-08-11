# DHSVM MakeLocalMetData Function Dependencies

Comprehensive dependency diagram for the `MakeLocalMetData` function in the DHSVM model.

## Key Findings:

1. **Function Call Hierarchy**: `MakeLocalMetData` is called from the main simulation loop in `MainDHSVM.c` for each grid cell and calls several utility functions for meteorological calculations.

2. **Main Function Dependencies**:
  - `LapseT()` - Temperature adjustment with elevation
  - `SeparateRadiation()` - Separates solar radiation into beam and diffuse components
  - `LapsePrecip()` - Precipitation adjustment with elevation
  - `SatVaporPressure()` - Calculates saturated vapor pressure using lookup table
  - `CalcSnowAlbedo()` - Calculates snow surface albedo

3. **Data Structure Dependencies**: The function takes 18+ input parameters including maps, weather station data, model configuration options, and various meteorological grids.

4. **Processing Flow**: The function follows a complex decision tree based on configuration options (MM5 vs station data, shading options, precipitation sources) and performs extensive meteorological calculations.

5. **File Dependencies**: Includes 7 header files and depends on 4 separate source files for the utility functions.

The function is central to DHSVM's meteorological processing, generating local meteorological conditions for each grid cell by interpolating or processing various data sources (weather stations, MM5 model output, radar precipitation) and applying topographic corrections, lapse rates, and physical property calculations.

This diagram should help you understand how `MakeLocalMetData` fits into the overall DHSVM architecture and its various dependencies for generating spatially distributed meteorological forcing data

## Function Call Hierarchy

```mermaid
graph TD
    A[MainDHSVM - Main Loop] --> B[MakeLocalMetData]
    B --> C[LapseT<br/>Temperature Lapse]
    B --> D[SeparateRadiation<br/>Separate Solar Components]
    B --> E[LapsePrecip<br/>Precipitation Lapse]
    B --> F[SatVaporPressure<br/>Vapor Pressure Calculation]
    B --> G[CalcSnowAlbedo<br/>Snow Albedo Calculation]
    
    C --> C1[Temperature adjustment<br/>with elevation]
    D --> D1[Beam radiation calculation]
    D --> D2[Diffuse radiation calculation]
    E --> E1[Precipitation adjustment<br/>with elevation]
    F --> F1[Saturated vapor pressure<br/>lookup table]
    G --> G1[Snow surface albedo<br/>based on age and temperature]
```

## Data Structure Dependencies

```mermaid
graph TB
    subgraph "Input Parameters"
        I1[MAPSIZE *Map<br/>Grid information]
        I2[OPTIONSTRUCT *Options<br/>Model configuration]
        I3[METLOCATION *Stat<br/>Weather station data]
        I4[PIXRAD *RadMap<br/>Radiation map]
        I5[PRECIPPIX *PrecipMap<br/>Precipitation map]
        I6[SNOWPIX *LocalSnow<br/>Snow conditions]
        I7[VEGPIX *VegMap<br/>Vegetation data]
        I8[MM5Input data<br/>Meteorological model data]
        I9[WindModel data<br/>Wind model data]
        I10[ShadowMap<br/>Topographic shading]
        I11[SkyViewMap<br/>Sky view factor]
    end
    
    subgraph "Output Structure"
        O1[PIXMET LocalMet<br/>Local meteorological data]
        O2[Temperature]
        O3[Humidity]
        O4[Wind Speed]
        O5[Solar Radiation]
        O6[Pressure]
        O7[Vapor Pressure]
    end
    
    I1 --> MLD[MakeLocalMetData]
    I2 --> MLD
    I3 --> MLD
    I4 --> MLD
    I5 --> MLD
    I6 --> MLD
    I7 --> MLD
    I8 --> MLD
    I9 --> MLD
    I10 --> MLD
    I11 --> MLD
    
    MLD --> O1
    O1 --> O2
    O1 --> O3
    O1 --> O4
    O1 --> O5
    O1 --> O6
    O1 --> O7
```

## Processing Flow Dependencies

```mermaid
graph LR
    subgraph "Configuration Check"
        A1[Check Options.MM5]
        A2[Check Options.Shading]
        A3[Check Options.WindSource]
        A4[Check Options.PrecipType]
        A5[Check Options.Prism]
    end
    
    subgraph "Data Source Processing"
        B1[MM5 Model Data<br/>Processing]
        B2[Weather Station<br/>Interpolation]
        B3[Radar Precipitation<br/>Processing]
        B4[PRISM Precipitation<br/>Processing]
    end
    
    subgraph "Meteorological Calculations"
        C1[Temperature Lapsing<br/>LapseT]
        C2[Radiation Separation<br/>SeparateRadiation]
        C3[Precipitation Lapsing<br/>LapsePrecip]
        C4[Topographic Shading<br/>Shadow correction]
        C5[Sky View Factor<br/>Diffuse radiation]
    end
    
    subgraph "Physical Property Calculations"
        D1[Saturated Vapor Pressure<br/>SatVaporPressure]
        D2[Air Pressure<br/>Barometric formula]
        D3[Air Density<br/>Ideal gas law]
        D4[Psychrometric Constant]
        D5[Vapor Pressure Deficit]
    end
    
    subgraph "Snow Calculations"
        E1[Snow Albedo<br/>CalcSnowAlbedo]
        E2[Rain/Snow Separation<br/>Temperature threshold]
        E3[Canopy Gap Processing]
    end
    
    A1 --> B1
    A1 --> B2
    A4 --> B3
    A5 --> B4
    
    B1 --> C2
    B2 --> C1
    B2 --> C3
    A2 --> C4
    A2 --> C5
    
    C1 --> D1
    C1 --> D2
    D1 --> D3
    D1 --> D4
    D1 --> D5
    
    C3 --> E2
    E2 --> E1
    E1 --> E3
```

## File Dependencies

```mermaid
graph TD
    subgraph "Header Files"
        H1[settings.h<br/>Configuration macros]
        H2[data.h<br/>Data structures]
        H3[snow.h<br/>Snow structures]
        H4[constants.h<br/>Physical constants]
        H5[functions.h<br/>Function prototypes]
        H6[rad.h<br/>Radiation structures]
        H7[DHSVMerror.h<br/>Error handling]
    end
    
    subgraph "Source Files"
        S1[LapseT.c<br/>Temperature & Precipitation lapse]
        S2[SeparateRadiation.c<br/>Solar radiation separation]
        S3[SatVaporPressure.c<br/>Vapor pressure lookup]
        S4[CalcSnowAlbedo.c<br/>Snow albedo calculation]
    end
    
    MLD[MakeLocalMetData.c] --> H1
    MLD --> H2
    MLD --> H3
    MLD --> H4
    MLD --> H5
    MLD --> H6
    MLD --> H7
    
    MLD --> S1
    MLD --> S2
    MLD --> S3
    MLD --> S4
```

## Key Constants Used

- **CP**: Specific heat of moist air (1013.0 J/(kg*C))
- **EPS**: Ratio of molecular weights (0.622)
- **SOLARCON**: Solar constant
- **CELL_PARTITION**: Number of vegetation types per cell (2)

## Major Decision Points

1. **MM5 vs Station Data**: Determines data source for meteorological variables
2. **Shading Options**: Controls topographic and canopy shading calculations
3. **Wind Source**: MODEL vs STATION for wind data
4. **Precipitation Type**: STATION vs RADAR for precipitation data
5. **PRISM**: Special precipitation interpolation method
6. **Precipitation Separation**: Combined vs separate rain/snow input

## Output Variables Computed

- Air temperature (°C)
- Relative humidity (%)
- Wind speed (m/s)
- Incoming solar radiation (W/m²)
  - Total, beam, and diffuse components
- Incoming longwave radiation (W/m²)
- Air pressure (Pa)
- Saturated vapor pressure (Pa)
- Actual vapor pressure (Pa)
- Vapor pressure deficit (Pa)
- Air density (kg/m³)
- Psychrometric constant
- Snow albedo
- Precipitation components (rain/snow)
