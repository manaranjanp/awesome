---
layout: post
title: "Predicting if a student will get admission or not"
date: 2016-05-25
description: "Building a classificaiton model using python sklearn library to predict student admission"
main-class: 'python'
published: true
color: '#637a91'
tags:
- python
- sklearn
- "data Science"
- classification
categories:
- python
introduction: "Building a classification model using python sklearn library to predict student admission"
---

# Predicting if a student will get admission or not

    import pandas as pd
    import numpy as np

    admission = pd.read_csv( "admission.csv" )

    admission.head()

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>admit</th>
      <th>gre</th>
      <th>gpa</th>
      <th>rank</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>380</td>
      <td>3.61</td>
      <td>3</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>660</td>
      <td>3.67</td>
      <td>3</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>800</td>
      <td>4.00</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1</td>
      <td>640</td>
      <td>3.19</td>
      <td>4</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0</td>
      <td>520</td>
      <td>2.93</td>
      <td>4</td>
    </tr>
  </tbody>
</table>
</div>




    admission.columns = ["admit", "gre", "gpa", "ranking" ]

## Does the ranking of the college impact the admissions and how much


    pd.crosstab( admission.admit, admission.ranking )




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>ranking</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
    </tr>
    <tr>
      <th>admit</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>28</td>
      <td>97</td>
      <td>93</td>
      <td>55</td>
    </tr>
    <tr>
      <th>1</th>
      <td>33</td>
      <td>54</td>
      <td>28</td>
      <td>12</td>
    </tr>
  </tbody>
</table>
</div>




    admit_by_rankings = pd.crosstab(
        admission.admit,
        admission.ranking ).apply( lambda x: x/x.sum(), axis = 0 )


    admit_by_rankings




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>ranking</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
    </tr>
    <tr>
      <th>admit</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.459016</td>
      <td>0.642384</td>
      <td>0.768595</td>
      <td>0.820896</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.540984</td>
      <td>0.357616</td>
      <td>0.231405</td>
      <td>0.179104</td>
    </tr>
  </tbody>
</table>
</div>




    admit_by_rankings = pd.DataFrame(
        admit_by_rankings.unstack() ).reset_index()


    admit_by_rankings




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>ranking</th>
      <th>admit</th>
      <th>0</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>0</td>
      <td>0.459016</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>1</td>
      <td>0.540984</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>0</td>
      <td>0.642384</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>1</td>
      <td>0.357616</td>
    </tr>
    <tr>
      <th>4</th>
      <td>3</td>
      <td>0</td>
      <td>0.768595</td>
    </tr>
    <tr>
      <th>5</th>
      <td>3</td>
      <td>1</td>
      <td>0.231405</td>
    </tr>
    <tr>
      <th>6</th>
      <td>4</td>
      <td>0</td>
      <td>0.820896</td>
    </tr>
    <tr>
      <th>7</th>
      <td>4</td>
      <td>1</td>
      <td>0.179104</td>
    </tr>
  </tbody>
</table>
</div>




    admit_by_rankings.columns = ["ranking", "admit", "total" ]


    import matplotlib as plt
    import seaborn as sn
    %matplotlib inline


    sn.barplot( admit_by_rankings.ranking, admit_by_rankings.total,
               hue = admit_by_rankings.admit )




    <matplotlib.axes._subplots.AxesSubplot at 0x91750f0>




![png](/assets/img/python/admit/output_13_1.png)


## Is the mean GRE and GPA score different for student who got admitted and who did not?


    gre_0 = admission[admission.admit == 0]["gre"]


    sn.distplot( gre_0 )




    <matplotlib.axes._subplots.AxesSubplot at 0x99034a8>




![png](/assets/img/python/admit/output_16_1.png)



    gre_1 = admission[admission.admit == 1]["gre"]


    sn.distplot( gre_1 )




    <matplotlib.axes._subplots.AxesSubplot at 0x9996b70>




![png](/assets/img/python/admit/output_18_1.png)



    g = sn.FacetGrid(admission, col="admit", size = 10)
    g.map(sn.distplot, "gre")




    <seaborn.axisgrid.FacetGrid at 0xaa3f2e8>




![png](/assets/img/python/admit/output_19_1.png)



    g = sn.FacetGrid(admission, col="admit", size = 10)
    g.map(sn.distplot, "gpa")




    <seaborn.axisgrid.FacetGrid at 0xb048a58>




![png](/assets/img/python/admit/output_20_1.png)



    sn.pairplot( admission[["gre", "gpa"]], size = 4 )




    <seaborn.axisgrid.PairGrid at 0xb048d68>




![png](/assets/img/python/admit/output_21_1.png)



    g = sn.FacetGrid(admission, row="ranking", size = 6)
    g.map(sn.distplot, "gre")




    <seaborn.axisgrid.FacetGrid at 0xbbb62e8>




![png](/assets/img/python/admit/output_22_1.png)



    sn.boxplot( "ranking", "gre", data = admission )




    <matplotlib.axes._subplots.AxesSubplot at 0xc052a20>




![png](/assets/img/python/admit/output_23_1.png)


## Building a Classification Model

### Convert the categorical variables into dummy variables


    def create_dummies( df, colname ):
        col_dummies = pd.get_dummies(df[colname], prefix=colname)
        col_dummies.drop(col_dummies.columns[0], axis=1, inplace=True)
        df = pd.concat([df, col_dummies], axis=1)
        df.drop( colname, axis = 1, inplace = True )
        return df


    admission_new = create_dummies( admission, "ranking" )


    admission_new.head()




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>admit</th>
      <th>gre</th>
      <th>gpa</th>
      <th>ranking_2</th>
      <th>ranking_3</th>
      <th>ranking_4</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>380</td>
      <td>3.61</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>660</td>
      <td>3.67</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>800</td>
      <td>4.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1</td>
      <td>640</td>
      <td>3.19</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0</td>
      <td>520</td>
      <td>2.93</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>




    from sklearn.linear_model import LogisticRegression


    logreg = LogisticRegression()


    admission_new.columns




    Index(['admit', 'gre', 'gpa', 'ranking_2', 'ranking_3', 'ranking_4'], dtype='object')




    feature_cols = ['gre', 'gpa', 'ranking_2', 'ranking_3', 'ranking_4']


    logreg.fit( admission_new[feature_cols], admission_new.admit )




    LogisticRegression(C=1.0, class_weight=None, dual=False, fit_intercept=True,
              intercept_scaling=1, max_iter=100, multi_class='ovr',
              penalty='l2', random_state=None, solver='liblinear', tol=0.0001,
              verbose=0)




    list( zip( feature_cols, logreg.coef_ ) )




    [('gre',
      array([ 0.00181821,  0.24353836, -0.60583825, -1.1749243 , -1.37839861]))]




    logreg.coef_




    array([[ 0.00181821,  0.24353836, -0.60583825, -1.1749243 , -1.37839861]])




    logreg.intercept_




    array([-1.8727875])




    admission_new["predicted_class"] = logreg.predict( admission_new[feature_cols] )


    admission_new = pd.concat( [admission_new,
                                pd.DataFrame(
                logreg.predict_proba( admission_new[feature_cols] ) )], axis = 1 )


    admission_new.head()




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>admit</th>
      <th>gre</th>
      <th>gpa</th>
      <th>ranking_2</th>
      <th>ranking_3</th>
      <th>ranking_4</th>
      <th>predicted_class</th>
      <th>0</th>
      <th>1</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>380</td>
      <td>3.61</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0.814212</td>
      <td>0.185788</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>660</td>
      <td>3.67</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0.721900</td>
      <td>0.278100</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>800</td>
      <td>4.00</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0.364488</td>
      <td>0.635512</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1</td>
      <td>640</td>
      <td>3.19</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0.787621</td>
      <td>0.212379</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0</td>
      <td>520</td>
      <td>2.93</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0.830918</td>
      <td>0.169082</td>
    </tr>
  </tbody>
</table>
</div>



## Evaluating the model - Confusion Matrix


    from sklearn import metrics


    cm = metrics.confusion_matrix( admission_new.admit,
                                  admission_new.predicted_class )


    cm




    array([[259,  14],
           [103,  24]])




    sn.heatmap(cm, annot=True,  fmt='.2f' );


![png](/assets/img/python/admit/output_44_0.png)


## Accuracy Score


    score = metrics.accuracy_score( admission_new.admit,
                                   admission_new.predicted_class )


    score




    0.70750000000000002



## ROC Curve

http://blog.yhathq.com/posts/roc-curves.html


    auc_score = metrics.roc_auc_score( admission_new.admit,
                                      admission_new.predicted_class )


    auc_score




    0.56884716333535223




    fpr, tpr, _ = metrics.roc_curve( admission_new.admit,
                                    admission_new.predicted_class )


    import matplotlib.pyplot as pyplt
    pyplt.figure()
    pyplt.plot( fpr, tpr, label='ROC curve (area = %0.2f)' % auc_score )
    pyplt.plot([0, 1], [0, 1], 'k--')
    pyplt.xlim([0.0, 1.0])
    pyplt.ylim([0.0, 1.05])
    pyplt.xlabel('False Positive Rate')
    pyplt.ylabel('True Positive Rate')
    pyplt.title('Receiver operating characteristic example')
    pyplt.legend(loc="lower right")
    pyplt.show()


![png](/assets/img/python/admit/output_52_0.png)



    print( metrics.classification_report( admission_new.admit,
                                         admission_new.predicted_class ) )

                 precision    recall  f1-score   support

              0       0.72      0.95      0.82       273
              1       0.63      0.19      0.29       127

    avg / total       0.69      0.71      0.65       400



## Cross validating the model


    from sklearn.cross_validation import cross_val_score


    logreg = LogisticRegression()
    X = admission_new[feature_cols]
    y = admission_new.admit
    scores = cross_val_score(logreg, X, y, cv=10, scoring='accuracy')


    scores




    array([ 0.82926829,  0.6097561 ,  0.73170732,  0.675     ,  0.725     ,
            0.675     ,  0.7       ,  0.66666667,  0.74358974,  0.66666667])




    scores.mean()




    0.70226547842401499



### Make note of lessons learnt in this exercise
