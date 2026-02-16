
### **Monthly Temperature Patterns in European Cities**
The objective of this project is to apply the PCA to a database with several variables in order to simplify its analysis and to be able to reach conclusions regarding the nature of the temperature of several European cities

Here we take a look at the data, delete the regions column as it is not of interest to us, and store the city names separately to have a num array with the numerical data for analysis.
![](https://i.imgur.com/0Sky0hz.png)

### 2. Exploring the Data
Now we are going to look for relationships between variables at a glance.
![](https://i.imgur.com/zrbXFTP.png)
![](https://i.imgur.com/bNVl6iF.png)
![](https://i.imgur.com/pWsltlT.png)
### The relationships found are as follows:
- Length has a low correlation with everything, amplitude in general also has a low correlation, latitude has a high correlation with everything, but negative, and as expected, the mean has a high correlation with all months.
    
- At last all months have a strong correlation between them.

### 3. Preprocessing the Data
To work in PCA, the data must be centered and scaled. and By centering and scaling the data, we find the mean at 0, the variance at 1, and the sum of all eigenvalues is equal to the number of variables, in our case 16.

### 4. Performing the PCA
Now that the data is standardized, we will apply PCA using the sklearn library. After this, we will choose the axes necessary to explain the variance of the data. To do this, we obtain the cumulative sum of the eigenvalues and plot it. What we are looking for now is to apply the three criteria we know to choose how many axes we are going to use to represent the data.
```python
from sklearn.decomposition import PCA

# Now apply PCA
pca = PCA(n_components=16)
pca.fit(Z_manual)

eigenvalues = (pca.singular_values_ ** 2) / (Z_manual.shape[0] - 1)

print(pca.explained_variance_ratio_)
print(pca.explained_variance_ratio_.cumsum())
print(pca.singular_values_)
eigenvalues = (pca.singular_values_ ** 2) / (Z.shape[0] - 1)
print('Eigenvalues sum:', eigenvalues.sum())
```

```
[7.80348705e-01 1.58875359e-01 3.99434398e-02 1.19297438e-02
 4.34297716e-03 1.96249738e-03 1.37958483e-03 5.01853907e-04
 3.50882178e-04 1.37597866e-04 8.82754178e-05 5.92781767e-05
 4.17325963e-05 2.24947089e-05 1.06237504e-05 4.95441561e-06]
[0.78034871 0.93922406 0.9791675  0.99109725 0.99544022 0.99740272
 0.99878231 0.99928416 0.99963504 0.99977264 0.99986092 0.99992019
 0.99996193 0.99998442 0.99999505 1.        ]
[20.60363307  9.29667657  4.66146235  2.54750478  1.5370685   1.03324662
  0.86631065  0.52250218  0.43689805  0.2735932   0.21913883  0.17957541
  0.1506736   0.11062152  0.07602184  0.05191534]
  

```
Eigenvalues sum: 16.0
A quick check of the sum of the eigenvalues, which should equal 1.
```python
n_componentes_90 = np.argmax(pca.explained_variance_ratio_.cumsum() >= 0.90) + 1
print(f'Components required for 90% variance: {n_componentes_90}')
```
```
Components required for 90% variance: 2
```
```python
kaiser_criterion = eigenvalues > 1
n_components_kaiser = sum(kaiser_criterion)

print("Eigenvalues:", eigenvalues)
print("Does it comply with Kaiser's rule?", kaiser_criterion)
print("Number of components to be preserved:", n_components_kaiser)
```
```
Eigenvalues: [1.24855793e+01 2.54200574e+00 6.39095037e-01 1.90875901e-01
 6.94876346e-02 3.13999581e-02 2.20733574e-02 8.02966251e-03
 5.61411484e-03 2.20156586e-03 1.41240668e-03 9.48450827e-04
 6.67721541e-04 3.59915342e-04 1.69980006e-04 7.92706498e-05]
Does it comply with Kaiser's rule? [ True  True False False False False False False False False False False
 False False False False]
Number of components to be preserved: 2
```
The elbow criterion suggests retaining 3 components.
![](https://i.imgur.com/m8xEoKd.png)
Now, to determine which axes to keep, the following criteria are used:

- First criterion: only the first two components would be used, as they achieve an alpha of more than 90%.
- Second criterion: with an average of more than 1, the first two axes meet the condition.
- Third criterion: using the maximum distance, the elbow was found in the third component.

The first two will be used because they exceed 90%.


### 5. Projection of Observations
The objective is to project the observations onto the principal axes.

Now what we are going to do is to project the observations on the main axes that we have already chosen, and we will try to find relationships between the different variables, whether differences or similarities.
```python
pca_2 = PCA(n_components=2)
pca_2.fit(Z) # Fit the PCA model first
c = pca_2.transform(Z)
print("Explained Variance Ratio:", pca_2.explained_variance_ratio_)
print("Cumulative Explained Variance Ratio:", pca_2.explained_variance_ratio_.cumsum())
print("Singular Values:", pca_2.singular_values_)
print("Singular vectors:", pca_2.components_)

```

```
Explained Variance Ratio: [0.78034871 0.15887536]
Cumulative Explained Variance Ratio: [0.78034871 0.93922406]
Singular Values: [20.60363307  9.29667657]
Singular vectors: [[ 0.25468826  0.26310598  0.27448705  0.27763509  0.25453384  0.24283806
   0.24211099  0.25389238  0.27748772  0.28099805  0.27471289  0.2598524
   0.28246243 -0.11880701 -0.25013664 -0.10596994]
 [-0.2593615  -0.21889002 -0.13397444  0.05733797  0.24459763  0.31144315
   0.30644495  0.25211295  0.11063296 -0.01550358 -0.11728693 -0.22295538
  -0.01174918  0.55714087 -0.16325338  0.36898063]]
```

```python
index_x = 0  # First principal component
index_y = 1  # Second principal component
plt.figure(figsize=(8, 6))

plt.scatter(c[:, index_x], c[:, index_y])
plt.title('Projection of Observations on the First Two Principal Components')
plt.xlabel('u{} (explained inertia: {}%)'.format(index_x+1, round(100*pca_2.explained_variance_ratio_[index_x],1)))
plt.ylabel('u{} (explained inertia: {}%)'.format(index_y+1, round(100*pca_2.explained_variance_ratio_[index_y],1)))
plt.grid(True)

# Label the points with city names
for i,(x,y) in enumerate(c[:,[index_x,index_y]]):
    plt.text(x, y,first_column[i] , fontsize=9)

plt.show()
```
![](https://i.imgur.com/EZUFFKu.png)
```python
def complete_contribution_analysis(c, cities, pca_model):
    """Complete contribution analysis with interpretation"""
    
    # 1. Calculate contributions
    contributions = (c**2) / (c.shape[0] - 1)
    contributions_pct = contributions / contributions.sum(axis=0) * 100
    
    # 2. Identify most influential observations
    top_pc1_pos = np.argsort(c[:, 0])[-3:][::-1]  # Top 3 positive PC1
    top_pc1_neg = np.argsort(c[:, 0])[:3]         # Top 3 negative PC1
    top_pc2_pos = np.argsort(c[:, 1])[-3:][::-1]  # Top 3 positive PC2
    top_pc2_neg = np.argsort(c[:, 1])[:3]         # Top 3 negative PC2
    
    print("=== ANALYSIS OF MOST INFLUENTIAL OBSERVATIONS ===\n")
    
    print("PC1 - POSITIVE OBSERVATIONS (Define positive end of axis):")
    for idx in top_pc1_pos:
        print(f"  • {cities[idx]}: coord={c[idx, 0]:.3f} ({contributions_pct[idx, 0]:.1f}%)")
    
    print("\nPC1 - NEGATIVE OBSERVATIONS (Define negative end of axis):")
    for idx in top_pc1_neg:
        print(f"  • {cities[idx]}: coord={c[idx, 0]:.3f} ({contributions_pct[idx, 0]:.1f}%)")
    
    print("\nPC2 - POSITIVE OBSERVATIONS (Define positive end of axis):")
    for idx in top_pc2_pos:
        print(f"  • {cities[idx]}: coord={c[idx, 1]:.3f} ({contributions_pct[idx, 1]:.1f}%)")
    
    print("\nPC2 - NEGATIVE OBSERVATIONS (Define negative end of axis):")
    for idx in top_pc2_neg:
        print(f"  • {cities[idx]}: coord={c[idx, 1]:.3f} ({contributions_pct[idx, 1]:.1f}%)")
    
    # 3. Qualitative interpretation
    print("\n=== INTERPRETATION ===")
    print("Cities with high contributions to PC1 define the most important")
    print("characteristics of that component. For example:")
    print("- If PC1 represents 'continentality', cities at opposite ends")
    print("  represent the most continental vs most maritime climates")
    print("- Cities with high contributions to PC2 define secondary")
    print("  but equally important characteristics")
    
    return contributions_pct

# Execute complete analysis
contributions = complete_contribution_analysis(c, first_column, pca_2)

```

```

=== ANALYSIS OF MOST INFLUENTIAL OBSERVATIONS ===

PC1 - POSITIVE OBSERVATIONS (Define positive end of axis):
  • Seville: coord=6.971 (11.4%)
  • Athenes: coord=6.435 (9.8%)
  • Palerme: coord=6.318 (9.4%)

PC1 - NEGATIVE OBSERVATIONS (Define negative end of axis):
  • Reykjavik: coord=-5.440 (7.0%)
  • St_Petersbourg: coord=-5.255 (6.5%)
  • Helsinki: coord=-5.102 (6.1%)

PC2 - POSITIVE OBSERVATIONS (Define positive end of axis):
  • Kiev: coord=2.740 (8.7%)
  • St_Petersbourg: coord=2.269 (6.0%)
  • Budapest: coord=2.123 (5.2%)

PC2 - NEGATIVE OBSERVATIONS (Define negative end of axis):
  • Dublin: coord=-3.207 (11.9%)
  • Reykjavik: coord=-3.173 (11.6%)
  • Edimbourg: coord=-3.020 (10.6%)

=== INTERPRETATION ===
Cities with high contributions to PC1 define the most important
characteristics of that component. For example:
- If PC1 represents 'continentality', cities at opposite ends
  repr

esent the most continental vs most maritime climates
- Cities with high contributions to PC2 define secondary
  but equally important characteristics
```
![](https://i.imgur.com/qQgZZPX.png)
```
=== TEMPERATURE ANALYSIS ===
Minimum temperature8.8°C
Maximum temperature: 19.4°C
Mean temperature: 13.3°C
```
Here a certain relationship can be observed between temperature and projection on the two chosen axes
![](https://i.imgur.com/SEePF1J.png)
```
=== LATITUDE ANALYSIS ===
Minimum latitude: 37.2°
Maximum latitude: 64.1°
```
There is a strong relationship between temperature and data representation using PCA, as well as latitude. Groups can be easily created by finding a relationship between temperature, latitude, and each of the axes.
```python
# Calculate the squared coordinates of the observations
squared_coords = c**2

# Calculate total squared distance from origin (for 2D)
total_squared_distance = squared_coords.sum(axis=1)

print("=== QUALITY OF REPRESENTATION (COS²) ===")
print("Observation\t\tCOS² Axis 1\tCOS² Axis 2")
print("-" * 50)

for i in range(len(total_squared_distance)):
    # CORRECTION: Calculate COS² for each axis
    cos2_axis1 = squared_coords[i, 0] / total_squared_distance[i]  # Quality on PC1
    cos2_axis2 = squared_coords[i, 1] / total_squared_distance[i]  # Quality on PC2
    
    print(f"{first_column[i]:<15}\t{cos2_axis1:.3f}\t\t{cos2_axis2:.3f}")
```
```
=== QUALITY OF REPRESENTATION (COS²) ===
Observation		COS² Axis 1	COS² Axis 2
--------------------------------------------------
Amsterdam      	0.058		0.942
Athenes        	0.918		0.082
Berlin         	0.996		0.004
Bruxelles      	0.000		1.000
Budapest       	0.071		0.929
Copenhague     	0.913		0.087
Dublin         	0.091		0.909
Helsinki       	0.971		0.029
Kiev           	0.509		0.491
Cracovie       	0.764		0.236
Lisbonne       	0.928		0.072
Londres        	0.044		0.956
Madrid         	0.949		0.051
Minsk          	0.832		0.168
Moscou         	0.812		0.188
Oslo           	1.000		0.000
Paris          	0.265		0.735
Prague         	0.627		0.373
Reykjavik      	0.746		0.254
Rome           	0.980		0.020
Sarajevo       	0.316		0.684
Sofia          	0.058		0.942
Stockholm      	1.000		0.000
Anvers         	0.003		0.997
Barcelone      	0.993		0.007
Bordeaux       	0.798		0.202
Edimbourg      	0.265		0.735
Francfort      	0.988		0.012
Geneve         	0.990		0.010
Genes          	1.000		0.000
Milan          	0.604		0.396
Palerme        	1.000		0.000
Seville        	0.996		0.004
St_Petersbourg 	0.843		0.157
Zurich         	0.972		0.028

All cities are well represented by one of the two main axes, yielding results very close to 1.
```
### 6. Projection of Variables (Correlation Circle)

The objective is now to project the variables inside the correlation circle onto the factorial axes.

1. Which formula from the course gives the coordinates of the variables in the considered subspace ?
    
2. Use the attribute `components_` of the `PCA` class to obtain the eigenvectors (or principal vectors) corresponding to the eigenvalues. Deduce the correlations between the original variables and the first principal factors.
    
3. Represent the projected variables on the relevant factorial plane(s) with line segments or arrows starting from the origin. Label each point and draw the correlation circle with the `Circle()` function from `matplotlib.pyplot`.
    
4. Find the strongly correlated variables (positively or negatively) and the weakly correlated variables.
    
5. Determine which variables contribute most to each principal component (both positively and negatively) and provide an interpretation of the different axes.
    
6. By combining the two projections (variables and observations), summarize the main insights provided by the PCA including:
    

- interpretation of each axis (meaning of new variables),
- groups of observations sharing similar characteristics.

The correlation circle tool will now be used in order to derive with greater certainty the correlation between the original variables and the main factors chosen, and an interpretation of the different axes is generated.

```python
def project_variables_correlation_circle(pca, feature_names, Z, c):
    """
    Project variables onto the correlation circle in factorial space
    
    Parameters:
    - pca: Fitted PCA model
    - feature_names: Names of the original variables
    - Z: Standardized data
    - c: Principal components (projected observations)
    """
    
    # Method 1: Using PCA components and explained variance
    variable_coords = pca.components_.T * np.sqrt(pca.explained_variance_)
    
    print("=== VARIABLE COORDINATES IN FACTORIAL PLANE ===")
    print("Variable\t\tPC1\t\tPC2")
    print("-" * 50)
    
    for i, name in enumerate(feature_names):
        print(f"{name:<15}\t{variable_coords[i, 0]:.3f}\t\t{variable_coords[i, 1]:.3f}")
    
    return variable_coords

# Get feature names (assuming you dropped 'Region')
feature_names = observations.columns.tolist()

# Calculate variable coordinates
variable_coordinates = project_variables_correlation_circle(pca_2, feature_names, Z, c)
```
```
== VARIABLE COORDINATES IN FACTORIAL PLANE ===
Variable		PC1		PC2
--------------------------------------------------
Janvier        	0.900		-0.414
Fevrier        	0.930		-0.349
Mars           	0.970		-0.214
Avril          	0.981		0.091
Mai            	0.899		0.390
Juin           	0.858		0.497
Juillet        	0.855		0.489
Aout           	0.897		0.402
Septembre      	0.981		0.176
Octobre        	0.993		-0.025
Novembre       	0.971		-0.187
Decembre       	0.918		-0.355
Moyenne        	0.998		-0.019
Amplitude      	-0.420		0.888
Latitude       	-0.884		-0.260
Longitude      	-0.374		0.588
```
The months have a very good representativity using the first axis especially, in the case of the amplitude, it seems to be very well represented by the second axis, the latitude very well represented but in a negative way with the first axis and the longitude really is not very well represented by either, this may mean that the longitude is not especially significant with respect to temperature.
![](https://i.imgur.com/7yztqoZ.png)
The main climate determinant is LATITUDE, which accounts for 78% of temperature variability. THERMAL AMPLITUDE (continentality) explains an additional 16% and acts independently. LONGITUDE has minimal influence on annual average temperatures.
```
=== VARIABLE CONTRIBUTIONS TO PRINCIPAL COMPONENTS ===

--- PC1 (78.0% variance) ---
Positive contributions (define positive end of PC1):
  • Moyenne: 0.282
  • Octobre: 0.281
  • Avril: 0.278
  • Septembre: 0.277
  • Novembre: 0.275

Negative contributions (define negative end of PC1):
  • Latitude: -0.250
  • Amplitude: -0.119
  • Longitude: -0.106
  • Juillet: 0.242
  • Juin: 0.243

--- PC2 (15.9% variance) ---
Positive contributions (define positive end of PC2):
  • Amplitude: 0.557
  • Longitude: 0.369
  • Juin: 0.311
  • Juillet: 0.306
  • Aout: 0.252

Negative contributions (define negative end of PC2):
  • Janvier: -0.259
  • Decembre: -0.223
  • Fevrier: -0.219
  • Latitude: -0.163
  • Mars: -0.134
```

```
=== COMBINED ANALYSIS: OBSERVATIONS AND VARIABLES ===

--- OBSERVATION GROUPS ---
Quadrant 1 (Positive PC1, Positive PC2): ['Athenes', 'Budapest', 'Madrid', 'Rome', 'Genes', 'Milan', 'Palerme', 'Seville']
Quadrant 2 (Negative PC1, Positive PC2): ['Helsinki', 'Kiev', 'Cracovie', 'Minsk', 'Moscou', 'Prague', 'Sarajevo', 'Sofia', 'Stockholm', 'Geneve', 'St_Petersbourg']
Quadrant 3 (Negative PC1, Negative PC2): ['Amsterdam', 'Berlin', 'Bruxelles', 'Copenhague', 'Dublin', 'Londres', 'Oslo', 'Reykjavik', 'Anvers', 'Edimbourg', 'Francfort', 'Zurich']
Quadrant 4 (Positive PC1, Negative PC2): ['Lisbonne', 'Paris', 'Barcelone', 'Bordeaux']

--- POTENTIAL CLUSTERS ---
High PC1 cluster: ['Athenes', 'Lisbonne', 'Madrid', 'Paris', 'Rome', 'Barcelone', 'Bordeaux', 'Genes', 'Milan', 'Palerme', 'Seville']
Low PC1 cluster: ['Copenhague', 'Helsinki', 'Kiev', 'Cracovie', 'Minsk', 'Moscou', 'Oslo', 'Reykjavik', 'Stockholm', 'Edimbourg', 'St_Petersbourg']
High PC2 cluster: ['Athenes', 'Budapest', 'Helsinki', 'Kiev', 'Cracovie', 'Minsk', 'Moscou', 'Sarajevo', 'Sofia', 'Milan', 'St_Petersbourg']
Low PC2 cluster: ['Amsterdam', 'Bruxelles', 'Copenhague', 'Dublin', 'Lisbonne', 'Londres', 'Paris', 'Reykjavik', 'Anvers', 'Bordeaux', 'Edimbourg']

--- GROUP CHARACTERISTICS ---
Variables defining PC1 positive end:
  • Moyenne: 0.998
  • Octobre: 0.993
  • Avril: 0.981

Variables defining PC1 negative end:
  • Latitude: -0.884
  • Amplitude: -0.420
  • Longitude: -0.374

Variables defining PC2 positive end:
  • Amplitude: 0.888
  • Longitude: 0.588
  • Juin: 0.497

Variables defining PC2 negative end:
  • Janvier: -0.414
  • Decembre: -0.355
  • Fevrier: -0.349
```

## 🔍 Key Findings
#### **PC1 (78.0%) - North-South Temperature Gradient**
- **Positive end**: Southern, warmer cities (Seville, Athens, Palermo)
- **Negative end**: Northern, colder cities (Reykjavik, St. Petersburg, Helsinki)
- **Key variables**: Average temperature, all monthly temperatures
- **Strong negative correlation**: Latitude
#### **PC2 (15.9%) - Continental vs Maritime Climate**
- **Positive end**: Continental cities (Kiev, Budapest, St. Petersburg)
- **Negative end**: Maritime cities (Dublin, Reykjavik, Edinburgh)
- **Key variables**: Thermal amplitude, summer months
### Climate Classification
European cities cluster into four main groups:

|Quadrant|Climate Type|Examples|
|---|---|---|
|**Q1** (Positive PC1, Positive PC2)|Warm + Continental|Athens, Budapest, Rome, Madrid|
|**Q2** (Negative PC1, Positive PC2)|Cold + Continental|Helsinki, Kiev, Moscow, Stockholm|
|**Q3** (Negative PC1, Negative PC2)|Cold + Maritime|Amsterdam, Berlin, Dublin, London|
|**Q4** (Positive PC1, Negative PC2)|Warm + Maritime|Lisbon, Paris, Barcelona, Bordeaux|

## 📈 Main Insights

1. Dual Climate Classification: European cities can be classified simultaneously by:

- North-South axis (average temperature)
    
- Continental-Maritime axis (thermal amplitude)
    

2. Clear Geographic Patterns:

- Northwest: Cold + Maritime
    
- Northeast: Cold + Continental
    
- Southwest: Warm + Maritime
    
- Southeast: Warm + Continental
    

3. Interesting exceptions:

- Budapest and Milan appear as "Continental South" despite not being the southernmost
    
- Geneve appears as "Continental North" for its altitude and remoteness from the sea
    

4. Confirmation of Climate Theories:

- Oceanic influence softens temperatures (low amplitude)
    
- Continentality extremiza temperatures (high amplitude)
    
- Latitude is the dominant factor in average temperatures