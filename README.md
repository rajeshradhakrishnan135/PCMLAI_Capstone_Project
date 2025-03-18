
### Fast-Charging of Electric Vehicles

** Rajesh Radhakrishnan (March 2025) **

#### Executive summary

The **goal** of this project is to estimate the Energy used for fast-charging of Electric Vehicles using the 
charging attributes as well as characteristics of the EV battery, namely the max requested power, change in the state of 
charge and Energy Capacity (Wh). 

The **findings** from the regression analysis indicates that the Energy Wh has a positive relationship with the Energy 
Capacity, Max Power Requested  and the change in the State of Charge. The SVM model gave the best accuracy score in classification 
of instances where the change in the state of charge is greater than 50%. 

#### Rationale

The cost of electric vehicle charging is proportional to the energy consumed for the charging session. Based on the regression model 
formulated in this project, the amount of energy consumed can be predicted based on the change in the state of charge and the 
peak rate of charging. This prediction would serve as the basis for estimating the cost incurred for the Level-3 or fast-charging 
session, which can then be provided to the user prior to charging.

#### Research Question

What is the relationship between Energy used for charging (Wh) and the charging characteristics of the Electric Vehicle's Battery?

** Background ** The growth of Electric Vehicles over the past decade has necessitated the development of EV charging infrastructure. 
Charging stations come with the following types of chargers: (a) Level 1 chargers = standard outlets (120V) typically in homes that 
provide slow charging overnight (b) Level 2 chargers = higher voltage (240V) and typically found in homes, workplaces and public areas, 
usually takes a few hours to fully charge (c) Level 3 chargers = also known as DC Fast Chargers, these high-speed charging stations 
rapidly charge the EV battery. The charging power ranges from 50kW to 350kW, and can achieve up to 80 percent of the charge in as 
little as 20-30 minutes.

CCS (Combined Charging System) plug are a type of charging plug for EV fast chargers that combine a Type 1 or Type 2 AC connector 
along with two additional DC pins, thus allowing for both AC and DC charging. CCS has become one of the most widely adopted fast-charging 
standards in North America and Europe.

#### Data Sources

Dataset: https://github.com/DESL-EPFL/Level-3-EV-charging-dataset/blob/main/Session_data.xlsx

Data from two CCS plugs has been collected from a Level 3 charging station from April 12, 2022 to July 4, 2023. The maximum 
power of the charging station is 172.5kW and two EVs can be charged at the same time. In such cases, the power capacity is 
equally allocated to the two EVs.

The session dataset consists of 1878 EV charging sessions, and includes data in the following fields:
1)	Session = ID of the session
2)	CCS = CCS1 or CCS2 plug
3)	Arrival = EV Arrival Time
4)	Departure = EV Departure Time
5)	Stay = EV charge duration (minutes)
6)	Energy = Charging Energy (Wh)
7)	Pmax = max AC charging power of the session
8)	Preq_max = max AC requested power
9)	Controlled Session = whether Pset < Preq anytime during the session
10)	TotalCapacity = total energy capacity of the EV’s battery (a-dim)
11)	BulkCapacity = usable energy capacity of the EV’s battery (a-dim)
12)	SOC arrival = State of Charge of EV’s battery on arrival (%)
13)	SOC departure = State of Charge of EV’s battery on departure (%)
14)	Energy Capacity = approx. capacity of EV’s battery (Wh)

The change in the state of charge (Change in SOC) is computed by subtracting SOC arrival from SOC departure. 

Description of the Dataset: https://github.com/DESL-EPFL/Level-3-EV-charging-dataset/

**Exploratory Data Analysis (see jupyter notebook)**: The data was found to be clean with no null or missing values. The heat map depicting 
the correlations among the fields shows that the Energy (Wh) has a 0.64 correlation with the Change in the State of Charge. The peak of the 
distribution of the Energy (Wh) values lies at 25,000 Wh and there are a few outliers exceeding 100,000 Wh. The histogram of the arrival state 
of charge shows that most of the EVs arrive with SOC less than 50%. The scatter plot of SOC Change and charging minutes indicates a positive 
correlation. CCS1 plug has a higher median Max Power requested than CCS2 plug.

#### Methodology

The dataset was partitioned into a training set = 80% of the data (for model training) and a test set = 20%  of the data (for validation and 
performance metrics). The primary tool for the analysis is Linear Regression without an intercept term (since Energy charged is zero if all 
the inputs are zero).

The **classification analysis** of instances where the change in the state of charge exceeded 50% was done using four models:
Logistic Regression, KNearestNeighbors (KNN), Decision Tree and Support Vector Machines (SVM)

Accuracy score is a suitable metric for the evaluation of these models since the dataset is balanced (the proportion of sessions with SOC 
change greater than 50% is 0.4004). It is the ratio of the total number of correct predictions to the total prediction count. Thus, Accuracy 
Score = (True Positives + True Negatives) / Total

#### Results

The **regression analysis** indicates that the Energy Wh has a positive relationship with the Energy Capacity, Max Power Requested 
and the change in the State of Charge. The coefficients on the variables CCS1 and CCS2 (type of plug for charging) are also positive.

The coefficient of determination (R-squared) for the Linear Regression is 0.8444 and the Adjusted R-squared is 0.8427 .
R-squared represents the proportion of variance in the dependent variable that is explained by the independent variables. Values 
that are closer to 1 indicates a better fit of the data by the model.

The Energy Wh for charging can thus be estimated from these variables prior to charging (assuming a good estimate of the Change in 
State of Charge required for the Battery can be determined), and thus it can be used to calculate the cost of the EV Charging session.

In the classification analysis of the change in the state of charge being greater than 50%, the following models were investigated:
1) Logistic Regression (Accuracy Score = 0.7660)
2) KNearestNeighbors (KNN) (Accuracy Score = 0.7314)
3) Decision Tree (Accuracy Score = 0.7819)
4) Support Vector Machines (SVM) (Accuracy Score = 0.7846)

The SVM model performed the best with Decision Tree a close second, followed by Logistic Regression and finally KNN.
In terms of time taken for the training step, KNN was the fastest (at 0.0097 sec) whereas SVM was the slowest (at 0.3258 sec). 

A **gridsearch** was performed for the SVM model by searching over the following hyper-parameters:
C : [0.1,1]
kernel : ["poly","rbf","sigmoid"]
degree : [3,5]
gamma : [0.01,0.1,1,'scale']
probability': [True]

The best SVM model was found to be: C = 1, degree = 3, gamma = 1, kernel = 'rbf', probability = True
And it was found that its Training accuracy (0.8249) was better than that of the base SVM case (0.8083) but the Test accuracy (0.7739) was poorer 
than the base SVM case (0.7846).

Analysis of the ROC (Receiver Operating Characteristic) Curve and AUC (Area Under the Curve) score of 0.8544 indicate that Logistic Regression 
provides a good discrimination (high classification accuracy) between the classes.

But the **SVM model** has the best accuracy among the four models and hence can be used (along with the other charging attributes) to identify the 
charging sessions where the Change in the State of Charge is expected to be more than 50 percent. These sessions provide the highest revenue to 
the charging station and the ability to identify them based on the inputs can help the charging station better serve these customers.

#### Next steps

The methodology from this analysis can be implemented at similar fast-charging stations in North America and Europe, to provide charging 
cost information to the Electric Vehicle customers (and thus a better customer service). Special incentives and/or promotions can be offered to EV 
customers identified as having a change in the state of charge over 50%, since they likely provide higher revenue to the station.

#### Outline of project

Link to the Jupyter Notebook = 
https://github.com/rajeshradhakrishnan135/PCMLAI_Capstone_Project/blob/main/Fast_Charging_of_Electric_Vehicles.ipynb

Link to the Dataset = 
https://github.com/DESL-EPFL/Level-3-EV-charging-dataset/blob/main/Session_data.xlsx

##### Contact and Further Information

Rajesh Radhakrishnan

Email: rajeshradhakrishnan135@gmail.com

[LinkedIn](linkedin.com/in/rajesh135)
