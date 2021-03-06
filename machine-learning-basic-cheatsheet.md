# Machine learning cheat-sheet (basic)

**Sampling**

> Generate random samples and then break the data into tests and training sets

- 2/3 of your dataset -> training set
- 1/3 of your dataset -> test set

**Methods**

- Supervised: making prediction from labeled data
- Unsupervised: clustering data

**Factor analysis**

A method used to explore datasets to find root causes that explain why data is acting a certain way

_factors(laten vars)_ = vars that are quite meaningful but are inferred and not directly observables

> **Assumptions**
> - Features are metrics
> - Continuous or ordinal
> r > 0.3 correlation between the features in your dataset
> N > 100 observation and > 5 observation per feature
> Sample is homogenous

**Single value decomposition (SVD)**

A linear algebra method that decomposes a matrix into three resultant to reduce information redundancy and noise

commonly used for principal component analysis

> Anatomy of SVD
> - A = u * s * v (matrices)
> - A = original matrix
> - u = left orthogonal matrix; holds important, non redundant info about observations
> - v = right orthogonal matrix; holds important info on features
> - S = diagonal matrix; contains all of the info about decomposition processes during the compression 

**Principal components**

Uncorrelated features that embody a dataset's important information (its variance) with the redundancy, noise, and outliers _stripped out_

> Usage
> - Fraud detection
> - Spam detectioin
> - Image and speech recognition

## Discover latent variables with Scikit-learn

### Factor analysis

**Imports**

```py
import pandas as pd
import numpy as np

import sklearn
from sklearn.decomposition import FactorAnalysis

from sklearn import datasets
```

**Implementations**

```py
factor = FactorAnalysis().fit(X)

pd.DataFrame(factor.components_, columns=variable_names)
```

### Principle components

**imports**

```py
import numpy as np
import pandas as pd

import matplotlib.pyplot as plt
import pylab as plt
import seaborn as sb
from IPython.display import Image
from IPython.core.display import HTML 
from pylab import rcParams

import sklearn
from sklearn import decomposition
from sklearn.decomposition import PCA
from sklearn import datasets
```

Sample data
```py
iris = datasets.load_iris()
X = iris.data
variable_names = iris.feature_names
X[0:10,]
```

**Explained variance ratio**
```py
pca = decomposition.PCA()
iris_pca = pca.fit_transform(X)

pca.explained_variance_ratio_

# sum
pca.explained_variance_ratio_.sum()
```

**Why ?**
- Explained variance ratio tells how much infomation is compressed into the first few components
- Helpful when deciding how many components to keep, looking at the percent of cummulative variance


### Extreme Value analysis

**Set up**
```py
df = pd.read_csv(
    filepath_or_buffer='C:/Users/Lillian Pierson/Desktop/Exercise Files/Ch05/05_01/iris.data.csv',
    header=None,
    sep=',')
df.columns=['Sepal Length','Sepal Width','Petal Length','Petal Width', 'Species']

X = df.ix[:,0:4].values
y = df.ix[:,4].values

df[:5]
```

#### Identifying outliers from Tukey boxplots

**Unvariate method**

```py
df.boxplot(return_type='dict')
plt.plota()

# from the boxplot, identify the quartile and normal ranges, then filter out potential outliers
# in this case, Sepal_width > 4 seems to be an outlier

Sepal_Width = X[:,1]
iris_outliers = (Sepal_Width > 4)
df[iris_outliers]

## apply Tukey outlier labeling

pd.options.display.float_format = '{:.1f}'.format
X_df = pd.DataFrame(X)
print X_df.describe()
```

**Variate method**

```py
sb.boxplot(x='Species', y='Sepal Length', data=df, palette='hls')

sb.pairplot(df, hue='Species', palette='hls')
```

#### Linear project methods for multi-variate data analysis

**DBSCAN for outlier detection**

Important DBSCAN model parameters
    - esps: the maximum distancec between two samples for them to be clustered in the same neighborhood (stat at esp = 0.1)
    - min_samples: the minimum number of samples in a neighborhood for a data point to qualify as a core point(stsart with very low sample size)
   

**imports**
```py
import pandas as pd

import matplotlib.pyplot as plt
from pylab import rcParams
import seaborn as sb

import sklearn
from sklearn.cluster import DBSCAN
from collections import Counter


df.columns=['Sepal Length','Sepal Width','Petal Length','Petal Width', 'Species']
data = df.ix[:,0:4].values
target = df.ix[:,4].values
df[:5]
```

**Implementation**

```py
model = DBSCAN(eps=0.8, min_samples=19).fit(data)
print model
```

**Visualization**

```py
outliers_df = pd.DataFrame(data)

print Counter(model.labels_)

print outliers_df[model.labels_ ==-1]

fig = plt.figure()
ax = fig.add_axes([.1, .1, 1, 1]) 

colors = model.labels_

ax.scatter(data[:,2], data[:,1], c=colors, s=120)
ax.set_xlabel('Petal Length')
ax.set_ylabel('Sepal Width')
plt.title('DBScan for Outlier Detection')
```


### K-mean clustering

**Base**
- Number of cluster center present (k)
- Nearest mean values (measure in Euclidian distance between observations) 
  
**Use cases**
- Market price and cost modelling
- Customer segmentation
- Insurance claim detection
- Hedge fund classification

**Keep in mind**
- Scale the variables
- Look at the scatterplot or the data table to estimate the number of centroid, cluster centers, to set for the k parameter for the model

**Set up**

```py
import numpy as np
import pandas as pd

import matplotlib.pyplot as plt

import sklearn
from sklearn.cluster import KMeans 
from mpl_toolkits.mplot3d import Axes3D
from sklearn.preprocessing import scale
import sklearn.metrics as sm # evaluate model
from sklearn import datasets
from sklearn.metrics import confusion_matrix, classification_report #use to evaluate the model

iris = dataset.load_iris()

X = scale(iris.data)
y = pd.DataFrame(iris.target)
variable_names = iris.feature_names
```

**Implementation**

```py
clustering = KMeans(n_clusters = 3, random_state = 5)
clustering.fit(X)

# plot the model

iris_df = pf.DataFrame(iris.data)
irs_df.columns = []
y.columns= ["Targets"]

color_theme = np.array(["darkgray", "lightsalmon", "powderblue"])

# first subplot
plot.subplot(1,2,1) # 1 row, 2 columns, first position

plot.scatter(x=iris_df.Petal_Length, y = iris_df.Petal_Width, c = color_theme[iris.target], s=50) 
plt.title("Ground Truth Classification")

plt.subplot(1,2,2)
plot.scatter(x=iris_df.Petal_Length, y = iris_df.Petal_Width, c = color_theme[clustering.labels], s=50) 
plt.title("K-Means classification")

# Relabel
relabel = np.choose(clustering.labels_, [2,0,1].astype(np.int64))
plot.subplot(1,2,1) # 1 row, 2 columns, first position

plot.scatter(x=iris_df.Petal_Length, y = iris_df.Petal_Width, c = color_theme[iris.target], s=50) 
plt.title("Ground Truth Classification")

plt.subplot(1,2,2)
plot.scatter(x=iris_df.Petal_Length, y = iris_df.Petal_Width, c = color_theme[clustering.labels], s=50) 
plt.title("K-Means classification")
```

**Evaluation**

```py
print classification_report(y, relabel)
```

- **Presicion**: a measure of the model's relevancy
- **Recall**: a measure of the model's completeness


### Hierachical Clustering

Hierachical clustering predict subgroups within data by
- finding distance between each data point and its nearest neighbors
- linking the most nearby neighbors
- 

**Use case**
- Hospital resource manangement
- Business process management
- Customer segmentation
- Social network analysis
  
**Hierachical Clustering parameters**
Distance Metrics
- Euclidian
- Manhattan
- Cosine

Linkage Parameters
- Ward
- Complete
- Average

_what to choose?_: Trial and error

**Imports**

```py
import scipy
from scipy.cluster.hierarchy import dendrogram, linkage
from scipy.cluster.hierarchy import fcluster
from scipy.cluster.hierarchy import cophenet
from scipy.spatial.distance import pdist

import matplotlib.pyplot as plt
from pylab import rcParams
import seaborn as sb

import sklearn
from sklearn.cluster import AgglomerativeClustering #Same as hierachical clustering
import sklearn.metrics as sm
```

#### implementation

**Using scipy to generate dendrogram**
```py
Z = linkage(X, "ward")
dendrogram(Z, truncate_mode = "lastp", p = 12, leaf_rotation = 45, leaf_front_size=15., show_contracted = True)


plt.title('Truncated Hierarchical Clustering Dendrogram')
plt.xlabel('Cluster Size')
plt.ylabel('Distance')

plt.axhline(y=500)
plt.axhline(y=150)
plt.show()
```

From what we've know about the data, we can make useful decision about the dataset using the dendrogram. Like how many clusters it should be

**Generating hierarchical clusters**

Since we are considering cars data set, which has 2 possible transmission: auto and manual. so we will set number of cluster to 2 and fit the data

```py
k = 2 # presumed 2 clusters


Hclustering = AgglomerativeClustering(n_clusters=k, affinity='euclidean', linkage='ward')
Hclustering.fit(X)

sm.accuracy_score(y, Hclustering.labels_)

Hclustering = AgglomerativeClustering(n_clusters=k, affinity='euclidean', linkage='complete')
Hclustering.fit(X)

sm.accuracy_score(y, Hclustering.labels_)

Hclustering = AgglomerativeClustering(n_clusters=k, affinity='euclidean', linkage='average')
Hclustering.fit(X)

sm.accuracy_score(y, Hclustering.labels_)

Hclustering = AgglomerativeClustering(n_clusters=k, affinity='manhattan', linkage='average')
Hclustering.fit(X)

sm.accuracy_score(y, Hclustering.labels_)
```



### Instanced-based learning K-Nearest neighbor classification

K-NN is a supervised classfier that memorizes observations from within a labeled test set to predict classfication labels for new, unlabeled observations

**Use cases**
- Stock price prediction
- Recommendation systems
- Credit risk anaylsis

**Assumptions**
- Data set has little noise
- Is Labeled
- Only contains relevant features
- has distinguishable subgroups
- **Avoid using K-NN on large dataset. It will takes a long time**

**Imports**

```py

import matplotlib.pyplot as plt
from pylab import rcParams

import urllib

import sklearn
from sklearn.neighbors import KNeighborsClassifier
from sklearn import neighbors
from sklearn import preprocessing
from sklearn.cross_validation import train_test_split
from sklearn import metrics
```
 
 **Implementation**

```py
X = preprocessing.scale(X_prime)
 X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=.33, random_state=17)

 # Building and training your model with training data
 clf = neighbors.KNeighborsClassifier()

clf.fit(X_train, y_train)
print(clf)

# Evaluation model's prediction against test dataset
y_expect = y_test
y_pred = clf.predict(X_test)

print(metrics.classification_report(y_expect, y_pred))
```

**Evaluation**
- Recall is a measure of your model's completeness
- High precision + low recall = Few results was returned (predicted), but many of the labels predictions returned were correct.

## Network Analysis

**Use cases**
- Social media marketing strategy
- Infrastructure system design
- Financial risk management
- Public health management

**Terms/Vocabulary**
- _Network_: a body of connected data that's evaluated during graph analysis
- _Graph_: A data visualization schematic depicting the data the comprises a network
- _nodes_: vertices around which a graph is formed
- _Edges_: the lines that connected the vertices within a graph structure
- _Directed Graph_(digraph): a graph where there is a direction assigned to each edge that connects a node
- _Graph size_: number of edges in a graph
- _Graph order_: the number of vertices in a graph
- _Degree_: the number of edges connected to a vertex, with loops counted twice (To measure the connectedness)
 
**Graph generators**
- Graph drawing algorithms
- Network analysis algorithms
- Algorithmic routing for graphs
- Graph Search algorithm
- SubGraph Algorithm

 ### Implementation

 **Imports**
```
 pip3 install networkx
```

```py
import networkx as nx
```

### Usage

**Simple**

```py
G = nx.graph()
nx.add_nodes_from([1,2,3,4,5,6,7,8,9,10,12])

nx.add_edges_from([(2,3), (2,4), (2,5)])

nx.draw(G)
```

**More complex**

```py
nx.draw_circular(G, node_color='bisque', with_labels=True)
```

**Identify graph properties**

```py
sum_stats = nx.info(G)
print sum_stats

# dictionary of degree 
print nx.degree(G)
```

**Using graph generator**

```py
G = nx.complete_graph(25) #25 nodes

G = nx.gnc_graph(7, seed=25) # 7 nodes, seed = random generator

ego_G  = nx.ego_graph(G, 3, radius = 5) # 3 nodes
```

### Simulate a social network

**Create graph object and edgelist**
```py
DG = nx.gn_graph(7, seed = 25)

for line in nx.generate_edgelist(DG, data = false) : print(line)
```

**Assign attributes**

```py
print DG.node[0]
DG.node[0]['name'] = 'Alice'

# Create a label dict
labeldict = {0: 'Alice', 1: 'Bob', ...}

```

### Social Network Analysis Metrics

- _Degree_: the connectedness
- _Successors_: nodes that could serve as backup and potentially replace an influential node in a network. The function of a sucessor node is to preserve the flow of influence throughout a network in the case where an important node is removed.
- _Neighbors_: adjacent nodes in a network

```py
# find the degree
DG.degree()

# Identify successor nodes
DG.successor(3)

# Identify neighbors
G.neighbors(4)
```

## Linear Regression

> Statistical machine learning method, used to quantify, make predictions based on relationships between numberical variables

- **Simple Linear regression**: one predictor and one predictant
- **Multiple linear regression**: multiple predictors and one predictant

**Use cases**
- Sales forecasting
- Resource consumption forcasting
- supply cost forcasting
- Telecom services lifecycle forecasting

**Assumption**
- Continuous numberic, not categorial
- Data is free of missing values and outliers
- Linear relationship between predictors and predictant
- All predictors are indenpendent of each other
- Residuals are normally distributed

### Implementation

**imports**

```py
import sklearn
from sklearn.linear_model import LinearRegression
from sklearn.preprocessing import scale
from collections import Counter

enroll = pd.read_csv(enroll_csv_address)
enroll.columns = ['year', 'roll', 'unem', 'hgrad', 'inc']

# first try to pairplot to find anomalies and correlation

sb.pairplot(enroll)

# then print correlation metrics using 
enroll.corr()

# isolate data
enroll_data = enroll.ix[:,(2,3)].values # unem, hgrad
enroll_target = enroll.ix[:, 1].values

# Scale the data
X,y  = scale(enroll, data), enroll_target

# check for missing values
missing_values = X == np.NAN
X[missing_values == True]

LinReg = LinearRegression(normalize = True)

LinReg.fit(X,y )
Linear.score()
```

## Logistic Regression

> a simple machine learning method, used to predic the value of a numeric categorial var based on its relationship with predictor variables

**Use case**
- Purchase propensity vs ad spend analysis
- customer Churn prediction
- employee attrition modelling
- Hazardous event prediction

**Assumption**
- free of missing values
- predictant var is binary or ordinal (categorical var with ordered values)
- Predictor are independent of each other
- at least 50 observation per predictor variables (to ensure a reliable results)

### Implementation

**immports**
```py
import sklearn
from sklearn.preprocessing import scale 
from sklearn.linear_model import LogisticRegression
from sklearn.cross_validation import train_test_split
from sklearn import metrics
from sklearn import preprocessing
```

```py
# Checking for independence between features
sb.regplot(x = 'draft', y = 'carb', data = cars, scatter=True)

drat = cars['drat']
carb = cars['carb']

# check for correlation between to value to prove independence
spearman_coefficient, p_value = spearmanr(drat, carb)


# Check for missing values
cars.isnull().sum()

# check if the target is binary or ordinal
sb.countplot(x='am', data=cars, palatte = 'plts')

# Check if the size of the dataset is sufficient
cars.info()

# Deploy and evaluating the model
X = scale(cars_data)
LogReg = LogisticRegression()
LogReg.fit(X, y)

print LogReg.score(X,y)

# Evluate metrics
y_pred = LogReg.predict(X)
from sklearn.metrics import classification_report
print(classification_report(y, y_pred))
```
## Naive Bayes

**Types of Naive Bayes**
- Multinomial: good for when your features (categorical or continuous) describe discrete frequency counts(e.g word counts)
- Bernoulli: good for making predictions from binary features
- Gaussian: good formaking predictions from normally distributed features


**Assumption**
- Predictors are independent of each other
- A priori assumption: this is an assumption that the past conditions still hold true. when we make predictions from historical values, we will get incorrect results if present circumstances have changed.
- All regression models maintain a priori assumption as well

**Use cases**
- Spam detection
- Customer classificiation
- Credit risk prediction
- Health risk prediction

### Implementation

**Imports**
```py
import sklearn
from sklearn.naive_bayes import BernoulliNB
from sklearn.naive_bayes import GaussianNB
from sklearn.naive_bayes import MultinomialNB
from sklearn.cross_validation import train_test_split
from sklearn import metrics
from sklearn.metrics import accuracy
```

**Use NB to predict spam**
```py
url = '' #mail data
raw_data = urllib.urlopen(url)
np.loadtxt(raw_data, delimiter =',')

X = dataset[:, 0:48]
y = dataset [:, -1] # get the last columns

X_train, X_test, y_train, y_test = train_test_split(X,y, test_size = .33, random_state = 17) 

# Bernoulli
BernNB = BernoulliNB(binarize = True)
BernNB.fit(X_train, y_train)
print(BernNB)

y_exprect = y_test
y_pred = BernNB.predict(X_test)
print accuracy_score(y_expect, y_pred)

# Multinomial
MutliNB = MultinomialNB()
MutliNB.fit(X_train, y_train)
print(MultiNB)

y_pred = MultiNB.predict(X_test)
print accuracy_score(y_expect, y_pred)

# Gaussian

GausNB = GaussianNB()
GausNB.fit(X_train, y_train)
print(GausNB)

y_pred = GausNB.predict(X_test)
print accuracy_score(y_expect, y_pred)

# Improve
# why 0.1 ? 
BernNB = BernoulliNB(binarize=0.1)
BernNB.fit(X_train, y_train)
print(BernNB)

y_exprect = y_test
y_pred = BernNB.predict(X_test)
print accuracy_score(y_expect, y_pred)

```

## Create Charts

**Plotly**
To create Plotly plots from NumPy objects, using the following attributes
- **Traces**: These are objects that describe a single variable of data in a graph, for example, a scatterplot or heatmap
- **Layouts**: You use these attributes to set layout elements for your plot, for example, the title, x-axis, or annotations.
  
**Install PlotLy**
```py
pip install Plotly
pip install cufflinks 
```

**imports**
```py
import cufflinks as cf
import plotly.plotly as py
import plotly.tools as tls
import plotly.graph_objs as go

tls.set_credentials_file[username=, api_key=]

```

### Creating line charts

**simple Line chart**
```py
a = np.linspace(start=0, stop=36, num=36)

np.random.seed(25)
b = np.random.uniform(low=.0, high=1.0, size=36)

trace = go.scatter(x=a, y=b)
data = [trace]

py.iplot(data, filename='basic-line-chart')
```

**A line chart with more than one variable plotted**
```py
x = [1,2,3,4,5,6,7,8]
y= [1,2,654,645,3]
z = [1,56,7,3,4,5]

trace0 = go.scatter(x=x, y=y, name='List Object', line = dict(width=5))
trace1 = go.scatter(x=x, y=z, name ='List Object', line = dict(width = 10))

data = [trace0, trace1]

layout  = dict(title = "Double Line Chart", xaxis=dict(title='x-axis'), yaxis=dict(title='y-axis'))

fig = dict(data=data, layout=layout)
print fig

py.iplot(fig, filename='styled-line-chart')
```

**Line chart from PandasDataframe**

```py
address = ''
cars = pd.read_csv(address)
cars.column = ['']
df = cars[['cyl', 'wt', 'mpg']]

layout = dict(title = 'Chart From Pandas DataFrame', xaxis=dict(title='x-axis'), yaxis = dict(title='y-title'))

df.iplot(filename='cf-simple-line-chart', layout=layout)
```

**Bar Charts**

```py
data = [go.Bar(x=[1,2,3,4,5,6,7], y = [1,2,3,5,6,7,8])]

layout = dict(title='Bar Chart',
                xaxis=dict(title='x-axis'), yaxis=dict(title='y-axis'))
py.iplot(data, filename='basic-bar-chart')
```

**Color theme**
```py
color_theme = dict(color = ['rgba(169,169,169,1)','',])
trace0 = go.Bar(x=[1,2,3,4,5,6], y=[1,2,4,5,6,4,7], marker = color_theme)
data = [trace0]
layout = go.Layout(title='Custom Colors')
fig = go.Figure(data= data, layout=layout)
py.iplot(fig, filename='color-bar-chart')
```

**Pie Charts**
```py
fig = {'data':[{'labels': ['bicycles', 'motorbike'],
                'values': [1,2], 'type':'pie'}],
        'layout': {'title': 'Simple Pie Chart'}}

py.iplot(fig)
```

## Statisitical Plots

**Stat Plots in Plotly**
- Histograms
- Boxplots
- Scatterplots

**Plotting Histograms from Pandas**
- Simple Hist chart: plot a seris object
- Multiple His chart: plot a dataframe (with overlapping, transparent, interactive hist)
- Subplot histograms: plot a dataframe in separate, clear, interactive subplots.

**Generating Scatterplots**
- the `mode` parameter should always be `markers`
- By _Default_, Plotly will draw lines between data points. So, if you want the points with no lines, you need to designate the plot mode as makers.

### Implementation

**Imports**
```py
import plotly.plotly as py
import plotly.tools as tls
import plotly.graph_objs as go
import sklearn
from sklearn.preprocessing import StandardScaler

//Set credential
tls.set_credentials_file(username=, api_key)
```

**Create Histograms**

```py
address =''
cars = pd.read_csv()
cars.column=['']
mpg = cars.mpg

mpg.iplot(kind='histogram', file_name = 'simple-history-chart')

cars_data = cars.ix[:, (1,3,4)].values
cars_data_std = StandardScaler().fit_transform(cars_data)
cars_select = pd.DataFrame(cars_data_std)
cars_select.columns= ['mpg', 'disp', 'hp']

cars_select.iplot(kind='histogram', filename='multiple-histogram-chart')

# Making subplots
cars_select.iplot(kind='histogram', filename='subplots-histogram-chart', subplots=True, shape=(3,1)) # 3 rows, 1 columns
```

**Create box plots**
```py
cars_select.iplot(kind ='box', filename='boxplots')
```

**Create a scatter plots**
```py
fig = {'data': [{'x': cars_select.mpg, 'y': cars_select.disp, 'mode':'markers', 'name':'mpg'},
                {'x': cars_select.hp, 'y': cars_select.disp, 'mode':'markers', 'name':'hp'}], 'layout':{'xaxis':{'title':''}, 'yaxis':{'title':'Standardized Displacement'}}}
```

### Plotly Maps

Plotting data on a map

**generating Choropleth**

```py
states['text'] = 'SATv'+ states['satv'].astype(str) + 'SATs' + states['states'].astype(str) + '<br>'+\
'State ' states['code']
data = [dict(type='choropleth', autocolorscale=False, locations=states['code'], z=states['dollars'], locationmode='USA-states', text=states['text'], colorscale='custom-colorscale', colorbar = dict(title='thousand dollars'))]

layout = dict(title='State Spending on Public Education, in $k/student', geo = dict(scope='usa', projection=dict(type='albers usa', showlakes = True, lakecolor='rbg(66,165,245)',),))

fig = dict(data=data, layout=layout)

py.iplot(fig, filename='d3-choroplot-map')
```

**Point maps**

```py

```