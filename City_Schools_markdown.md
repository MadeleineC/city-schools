

```python
#TRENDS
#1: On average, schools that spend more per pupil actually have a worse overall pass rate.
#2: Schools that are smaller have, on average, stronger overall performance. 
#3: On average, charter schools outperform District schools. 
#**My next recommendation for the school district would to look at performance broken down special groups (English as a Second Language, Free and Reduced Lunch, Special Education etc.), as this may provide nuance to the above-mentioned trends.
```


```python
import pandas as pd
import numpy as np
```


```python
schools_csv = "raw_data/schools_complete.csv"
students_csv = "raw_data/students_complete.csv"
```


```python
school_df = pd.read_csv(schools_csv)
school_df.head()

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>School ID</th>
      <th>name</th>
      <th>type</th>
      <th>size</th>
      <th>budget</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>Huang High School</td>
      <td>District</td>
      <td>2917</td>
      <td>1910635</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>Figueroa High School</td>
      <td>District</td>
      <td>2949</td>
      <td>1884411</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>Shelton High School</td>
      <td>Charter</td>
      <td>1761</td>
      <td>1056600</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>Hernandez High School</td>
      <td>District</td>
      <td>4635</td>
      <td>3022020</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>Griffin High School</td>
      <td>Charter</td>
      <td>1468</td>
      <td>917500</td>
    </tr>
  </tbody>
</table>
</div>




```python
student_df = pd.read_csv(students_csv)
student_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Student ID</th>
      <th>name</th>
      <th>gender</th>
      <th>grade</th>
      <th>school</th>
      <th>reading_score</th>
      <th>math_score</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>Paul Bradley</td>
      <td>M</td>
      <td>9th</td>
      <td>Huang High School</td>
      <td>66</td>
      <td>79</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>Victor Smith</td>
      <td>M</td>
      <td>12th</td>
      <td>Huang High School</td>
      <td>94</td>
      <td>61</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>Kevin Rodriguez</td>
      <td>M</td>
      <td>12th</td>
      <td>Huang High School</td>
      <td>90</td>
      <td>60</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>Dr. Richard Scott</td>
      <td>M</td>
      <td>12th</td>
      <td>Huang High School</td>
      <td>67</td>
      <td>58</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>Bonnie Ray</td>
      <td>F</td>
      <td>9th</td>
      <td>Huang High School</td>
      <td>97</td>
      <td>84</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Create a high level snapshot (in table form) of the district's key metrics:
#Total Schools
total_schools = len(school_df)
#Total Students
total_students = len(student_df)
#Total Budget
total_budget = school_df["budget"].sum()
#Average Math Score
avg_math = student_df["math_score"].mean()
#Average Reading Score
avg_reading = student_df["reading_score"].mean()
#% Passing Math
percent_passing_math = student_df.loc[student_df["math_score"] > 70].count()["Student ID"]/float(total_students)*100
#total_passing_math = student_df.loc[student_df["math_score"] > 70]
#percent_passing_math = len(student_df.loc[student_df["math_score"] >= 70])/total_students*100
#% Passing Reading
percent_passing_reading = len(student_df.loc[student_df["reading_score"] > 70])/total_students*100
#Overall Passing Rate (Average of the above two)
overall_passing_rate = (percent_passing_math+percent_passing_reading)/2

district_summary_df = pd.DataFrame({"Number of Schools": total_schools, 
                                    "Total Students": total_students, 
                                    "Total Budget": total_budget, 
                                    "Average Math Score": avg_math,
                                    "Math Pass Rate": percent_passing_math,
                                    "Average Reading Score": avg_reading,
                                    "Reading Pass Rate": percent_passing_reading,
                                    "Overall Pass Rate": overall_passing_rate,
                                   }, index=[0])
district_summary_df["Total Budget"] = district_summary_df["Total Budget"].map("$ {:,.2f}".format)
district_summary_df["Average Math Score"] = district_summary_df["Average Math Score"].map("{:,.2f}".format)
district_summary_df["Average Reading Score"] = district_summary_df["Average Reading Score"].map("{:,.2f}".format)
district_summary_df["Math Pass Rate"] = district_summary_df["Math Pass Rate"].map("{0:,.2f}%".format)
district_summary_df["Reading Pass Rate"] = district_summary_df["Reading Pass Rate"].map("{0:,.2f}%".format)
district_summary_df["Overall Pass Rate"] = district_summary_df["Overall Pass Rate"].map("{0:,.2f}%".format)

district_summary_df = district_summary_df[["Number of Schools", 
                                    "Total Students", 
                                    "Total Budget", 
                                    "Average Math Score",
                                    "Math Pass Rate",
                                    "Average Reading Score",
                                    "Reading Pass Rate",
                                    "Overall Pass Rate"]]
district_summary_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Number of Schools</th>
      <th>Total Students</th>
      <th>Total Budget</th>
      <th>Average Math Score</th>
      <th>Math Pass Rate</th>
      <th>Average Reading Score</th>
      <th>Reading Pass Rate</th>
      <th>Overall Pass Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>15</td>
      <td>39170</td>
      <td>$ 24,649,428.00</td>
      <td>78.99</td>
      <td>72.39%</td>
      <td>81.88</td>
      <td>82.97%</td>
      <td>77.68%</td>
    </tr>
  </tbody>
</table>
</div>




```python
# School Summary
students_grouped = student_df.groupby(["school"])
new_school_df = pd.DataFrame({})

# School Name
# School Type
# Total Students
# Total School Budget
# Per Student Budget
per_student_budget = school_df["budget"]/school_df["size"]
school_df["Per Student Budget"] = per_student_budget
# Average Math Score
avg_math_score_list_by_school = students_grouped["math_score"].mean()
new_school_df["Avg Math Score"] = avg_math_score_list_by_school
# Average Reading Score
avg_reading_score_list_by_school = students_grouped["reading_score"].mean()
new_school_df["Avg Reading Score"] = avg_reading_score_list_by_school

#Count of Students Passing
students_passing_reading = student_df.loc[student_df["reading_score"] > 70]
students_passing_reading_groupby_school = students_passing_reading.groupby(["school"]).count()
new_school_df["Ss Passing reading"] = students_passing_reading_groupby_school["Student ID"]

students_passing_math = student_df.loc[student_df["math_score"] > 70]
students_passing_math_groupby_school = students_passing_math.groupby(["school"]).count()
new_school_df["Ss Passing Math"] = students_passing_math_groupby_school["Student ID"]


#Merge Data Frames
school_df_with_scores = pd.merge(school_df, new_school_df, left_on = 'name', right_index=True)

# % Passing Math
# % Passing Reading
# Overall Passing Rate (Average of the above two)
percent_passing_math_school_list = school_df_with_scores["Ss Passing Math"]/school_df_with_scores["size"]*100
percent_passing_reading_school_list = school_df_with_scores["Ss Passing reading"]/school_df_with_scores["size"]*100
overall_pass_rate_school_list = (percent_passing_math_school_list+percent_passing_reading_school_list)/2
school_df_with_scores["% Passing Math"] = percent_passing_math_school_list
school_df_with_scores["% Passing Reading"] = percent_passing_reading_school_list
school_df_with_scores["Overall Pass Rate"] = overall_pass_rate_school_list


school_df_with_scores = school_df_with_scores[["School ID", 
                                    "name", 
                                    "type", 
                                    "size",
                                    "budget",
                                    "Per Student Budget",
                                    "Avg Math Score",
                                    "Ss Passing Math",
                                    "% Passing Math",
                                    "Avg Reading Score",
                                    "Ss Passing reading",
                                    "% Passing Reading",
                                    "Overall Pass Rate"]]

school_df_with_scores["budget"] = school_df_with_scores["budget"].map("$ {:,.0f}".format)
school_df_with_scores["Per Student Budget"] = school_df_with_scores["Per Student Budget"].map("$ {:,.0f}".format)
school_df_with_scores["Avg Math Score"] = school_df_with_scores["Avg Math Score"].map("{:,.2f}".format)
school_df_with_scores["Avg Reading Score"] = school_df_with_scores["Avg Reading Score"].map("{:,.2f}".format)
school_df_with_scores["% Passing Math"] = school_df_with_scores["% Passing Math"].map("{0:,.2f}%".format)
school_df_with_scores["% Passing Reading"] = school_df_with_scores["% Passing Reading"].map("{0:,.2f}%".format)
school_df_with_scores["Overall Pass Rate"] = school_df_with_scores["Overall Pass Rate"].map("{0:,.2f}%".format)

#create data frame with counts by school
school_df_with_scores





```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>School ID</th>
      <th>name</th>
      <th>type</th>
      <th>size</th>
      <th>budget</th>
      <th>Per Student Budget</th>
      <th>Avg Math Score</th>
      <th>Ss Passing Math</th>
      <th>% Passing Math</th>
      <th>Avg Reading Score</th>
      <th>Ss Passing reading</th>
      <th>% Passing Reading</th>
      <th>Overall Pass Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>Huang High School</td>
      <td>District</td>
      <td>2917</td>
      <td>$ 1,910,635</td>
      <td>$ 655</td>
      <td>76.63</td>
      <td>1847</td>
      <td>63.32%</td>
      <td>81.18</td>
      <td>2299</td>
      <td>78.81%</td>
      <td>71.07%</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>Figueroa High School</td>
      <td>District</td>
      <td>2949</td>
      <td>$ 1,884,411</td>
      <td>$ 639</td>
      <td>76.71</td>
      <td>1880</td>
      <td>63.75%</td>
      <td>81.16</td>
      <td>2313</td>
      <td>78.43%</td>
      <td>71.09%</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>Shelton High School</td>
      <td>Charter</td>
      <td>1761</td>
      <td>$ 1,056,600</td>
      <td>$ 600</td>
      <td>83.36</td>
      <td>1583</td>
      <td>89.89%</td>
      <td>83.73</td>
      <td>1631</td>
      <td>92.62%</td>
      <td>91.25%</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>Hernandez High School</td>
      <td>District</td>
      <td>4635</td>
      <td>$ 3,022,020</td>
      <td>$ 652</td>
      <td>77.29</td>
      <td>3001</td>
      <td>64.75%</td>
      <td>80.93</td>
      <td>3624</td>
      <td>78.19%</td>
      <td>71.47%</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>Griffin High School</td>
      <td>Charter</td>
      <td>1468</td>
      <td>$ 917,500</td>
      <td>$ 625</td>
      <td>83.35</td>
      <td>1317</td>
      <td>89.71%</td>
      <td>83.82</td>
      <td>1371</td>
      <td>93.39%</td>
      <td>91.55%</td>
    </tr>
    <tr>
      <th>5</th>
      <td>5</td>
      <td>Wilson High School</td>
      <td>Charter</td>
      <td>2283</td>
      <td>$ 1,319,574</td>
      <td>$ 578</td>
      <td>83.27</td>
      <td>2076</td>
      <td>90.93%</td>
      <td>83.99</td>
      <td>2129</td>
      <td>93.25%</td>
      <td>92.09%</td>
    </tr>
    <tr>
      <th>6</th>
      <td>6</td>
      <td>Cabrera High School</td>
      <td>Charter</td>
      <td>1858</td>
      <td>$ 1,081,356</td>
      <td>$ 582</td>
      <td>83.06</td>
      <td>1664</td>
      <td>89.56%</td>
      <td>83.98</td>
      <td>1744</td>
      <td>93.86%</td>
      <td>91.71%</td>
    </tr>
    <tr>
      <th>7</th>
      <td>7</td>
      <td>Bailey High School</td>
      <td>District</td>
      <td>4976</td>
      <td>$ 3,124,928</td>
      <td>$ 628</td>
      <td>77.05</td>
      <td>3216</td>
      <td>64.63%</td>
      <td>81.03</td>
      <td>3946</td>
      <td>79.30%</td>
      <td>71.97%</td>
    </tr>
    <tr>
      <th>8</th>
      <td>8</td>
      <td>Holden High School</td>
      <td>Charter</td>
      <td>427</td>
      <td>$ 248,087</td>
      <td>$ 581</td>
      <td>83.80</td>
      <td>387</td>
      <td>90.63%</td>
      <td>83.81</td>
      <td>396</td>
      <td>92.74%</td>
      <td>91.69%</td>
    </tr>
    <tr>
      <th>9</th>
      <td>9</td>
      <td>Pena High School</td>
      <td>Charter</td>
      <td>962</td>
      <td>$ 585,858</td>
      <td>$ 609</td>
      <td>83.84</td>
      <td>882</td>
      <td>91.68%</td>
      <td>84.04</td>
      <td>887</td>
      <td>92.20%</td>
      <td>91.94%</td>
    </tr>
    <tr>
      <th>10</th>
      <td>10</td>
      <td>Wright High School</td>
      <td>Charter</td>
      <td>1800</td>
      <td>$ 1,049,400</td>
      <td>$ 583</td>
      <td>83.68</td>
      <td>1625</td>
      <td>90.28%</td>
      <td>83.95</td>
      <td>1682</td>
      <td>93.44%</td>
      <td>91.86%</td>
    </tr>
    <tr>
      <th>11</th>
      <td>11</td>
      <td>Rodriguez High School</td>
      <td>District</td>
      <td>3999</td>
      <td>$ 2,547,363</td>
      <td>$ 637</td>
      <td>76.84</td>
      <td>2562</td>
      <td>64.07%</td>
      <td>80.74</td>
      <td>3109</td>
      <td>77.74%</td>
      <td>70.91%</td>
    </tr>
    <tr>
      <th>12</th>
      <td>12</td>
      <td>Johnson High School</td>
      <td>District</td>
      <td>4761</td>
      <td>$ 3,094,650</td>
      <td>$ 650</td>
      <td>77.07</td>
      <td>3040</td>
      <td>63.85%</td>
      <td>80.97</td>
      <td>3727</td>
      <td>78.28%</td>
      <td>71.07%</td>
    </tr>
    <tr>
      <th>13</th>
      <td>13</td>
      <td>Ford High School</td>
      <td>District</td>
      <td>2739</td>
      <td>$ 1,763,916</td>
      <td>$ 644</td>
      <td>77.10</td>
      <td>1801</td>
      <td>65.75%</td>
      <td>80.75</td>
      <td>2123</td>
      <td>77.51%</td>
      <td>71.63%</td>
    </tr>
    <tr>
      <th>14</th>
      <td>14</td>
      <td>Thomas High School</td>
      <td>Charter</td>
      <td>1635</td>
      <td>$ 1,043,130</td>
      <td>$ 638</td>
      <td>83.42</td>
      <td>1475</td>
      <td>90.21%</td>
      <td>83.85</td>
      <td>1519</td>
      <td>92.91%</td>
      <td>91.56%</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Top 5 Performers
schools_sorted_by_pass_rate = school_df_with_scores.sort_values("Overall Pass Rate", ascending=False)
schools_sorted_by_pass_rate.head(5)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>School ID</th>
      <th>name</th>
      <th>type</th>
      <th>size</th>
      <th>budget</th>
      <th>Per Student Budget</th>
      <th>Avg Math Score</th>
      <th>Ss Passing Math</th>
      <th>% Passing Math</th>
      <th>Avg Reading Score</th>
      <th>Ss Passing reading</th>
      <th>% Passing Reading</th>
      <th>Overall Pass Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>5</th>
      <td>5</td>
      <td>Wilson High School</td>
      <td>Charter</td>
      <td>2283</td>
      <td>$ 1,319,574</td>
      <td>$ 578</td>
      <td>83.27</td>
      <td>2076</td>
      <td>90.93%</td>
      <td>83.99</td>
      <td>2129</td>
      <td>93.25%</td>
      <td>92.09%</td>
    </tr>
    <tr>
      <th>9</th>
      <td>9</td>
      <td>Pena High School</td>
      <td>Charter</td>
      <td>962</td>
      <td>$ 585,858</td>
      <td>$ 609</td>
      <td>83.84</td>
      <td>882</td>
      <td>91.68%</td>
      <td>84.04</td>
      <td>887</td>
      <td>92.20%</td>
      <td>91.94%</td>
    </tr>
    <tr>
      <th>10</th>
      <td>10</td>
      <td>Wright High School</td>
      <td>Charter</td>
      <td>1800</td>
      <td>$ 1,049,400</td>
      <td>$ 583</td>
      <td>83.68</td>
      <td>1625</td>
      <td>90.28%</td>
      <td>83.95</td>
      <td>1682</td>
      <td>93.44%</td>
      <td>91.86%</td>
    </tr>
    <tr>
      <th>6</th>
      <td>6</td>
      <td>Cabrera High School</td>
      <td>Charter</td>
      <td>1858</td>
      <td>$ 1,081,356</td>
      <td>$ 582</td>
      <td>83.06</td>
      <td>1664</td>
      <td>89.56%</td>
      <td>83.98</td>
      <td>1744</td>
      <td>93.86%</td>
      <td>91.71%</td>
    </tr>
    <tr>
      <th>8</th>
      <td>8</td>
      <td>Holden High School</td>
      <td>Charter</td>
      <td>427</td>
      <td>$ 248,087</td>
      <td>$ 581</td>
      <td>83.80</td>
      <td>387</td>
      <td>90.63%</td>
      <td>83.81</td>
      <td>396</td>
      <td>92.74%</td>
      <td>91.69%</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Bottom 5 Performers
schools_sorted_by_pass_rate = school_df_with_scores.sort_values("Overall Pass Rate", ascending=True)
schools_sorted_by_pass_rate.head(5)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>School ID</th>
      <th>name</th>
      <th>type</th>
      <th>size</th>
      <th>budget</th>
      <th>Per Student Budget</th>
      <th>Avg Math Score</th>
      <th>Ss Passing Math</th>
      <th>% Passing Math</th>
      <th>Avg Reading Score</th>
      <th>Ss Passing reading</th>
      <th>% Passing Reading</th>
      <th>Overall Pass Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>11</th>
      <td>11</td>
      <td>Rodriguez High School</td>
      <td>District</td>
      <td>3999</td>
      <td>$ 2,547,363</td>
      <td>$ 637</td>
      <td>76.84</td>
      <td>2562</td>
      <td>64.07%</td>
      <td>80.74</td>
      <td>3109</td>
      <td>77.74%</td>
      <td>70.91%</td>
    </tr>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>Huang High School</td>
      <td>District</td>
      <td>2917</td>
      <td>$ 1,910,635</td>
      <td>$ 655</td>
      <td>76.63</td>
      <td>1847</td>
      <td>63.32%</td>
      <td>81.18</td>
      <td>2299</td>
      <td>78.81%</td>
      <td>71.07%</td>
    </tr>
    <tr>
      <th>12</th>
      <td>12</td>
      <td>Johnson High School</td>
      <td>District</td>
      <td>4761</td>
      <td>$ 3,094,650</td>
      <td>$ 650</td>
      <td>77.07</td>
      <td>3040</td>
      <td>63.85%</td>
      <td>80.97</td>
      <td>3727</td>
      <td>78.28%</td>
      <td>71.07%</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>Figueroa High School</td>
      <td>District</td>
      <td>2949</td>
      <td>$ 1,884,411</td>
      <td>$ 639</td>
      <td>76.71</td>
      <td>1880</td>
      <td>63.75%</td>
      <td>81.16</td>
      <td>2313</td>
      <td>78.43%</td>
      <td>71.09%</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>Hernandez High School</td>
      <td>District</td>
      <td>4635</td>
      <td>$ 3,022,020</td>
      <td>$ 652</td>
      <td>77.29</td>
      <td>3001</td>
      <td>64.75%</td>
      <td>80.93</td>
      <td>3624</td>
      <td>78.19%</td>
      <td>71.47%</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Avg Math Pass Rates by School and Grade
students_grouped_by_grade = student_df.groupby(["school","grade"], as_index=False)
avg_math_score_by_grade = students_grouped_by_grade["math_score"].mean()
avg_math_score_by_grade
math_table = avg_math_score_by_grade.pivot(index="school", columns="grade", values= "math_score")
math_table = math_table[["9th",
                        "10th",
                        "11th",
                        "12th"]]
math_table.round(2)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>grade</th>
      <th>9th</th>
      <th>10th</th>
      <th>11th</th>
      <th>12th</th>
    </tr>
    <tr>
      <th>school</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Bailey High School</th>
      <td>77.08</td>
      <td>77.00</td>
      <td>77.52</td>
      <td>76.49</td>
    </tr>
    <tr>
      <th>Cabrera High School</th>
      <td>83.09</td>
      <td>83.15</td>
      <td>82.77</td>
      <td>83.28</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>76.40</td>
      <td>76.54</td>
      <td>76.88</td>
      <td>77.15</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>77.36</td>
      <td>77.67</td>
      <td>76.92</td>
      <td>76.18</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>82.04</td>
      <td>84.23</td>
      <td>83.84</td>
      <td>83.36</td>
    </tr>
    <tr>
      <th>Hernandez High School</th>
      <td>77.44</td>
      <td>77.34</td>
      <td>77.14</td>
      <td>77.19</td>
    </tr>
    <tr>
      <th>Holden High School</th>
      <td>83.79</td>
      <td>83.43</td>
      <td>85.00</td>
      <td>82.86</td>
    </tr>
    <tr>
      <th>Huang High School</th>
      <td>77.03</td>
      <td>75.91</td>
      <td>76.45</td>
      <td>77.23</td>
    </tr>
    <tr>
      <th>Johnson High School</th>
      <td>77.19</td>
      <td>76.69</td>
      <td>77.49</td>
      <td>76.86</td>
    </tr>
    <tr>
      <th>Pena High School</th>
      <td>83.63</td>
      <td>83.37</td>
      <td>84.33</td>
      <td>84.12</td>
    </tr>
    <tr>
      <th>Rodriguez High School</th>
      <td>76.86</td>
      <td>76.61</td>
      <td>76.40</td>
      <td>77.69</td>
    </tr>
    <tr>
      <th>Shelton High School</th>
      <td>83.42</td>
      <td>82.92</td>
      <td>83.38</td>
      <td>83.78</td>
    </tr>
    <tr>
      <th>Thomas High School</th>
      <td>83.59</td>
      <td>83.09</td>
      <td>83.50</td>
      <td>83.50</td>
    </tr>
    <tr>
      <th>Wilson High School</th>
      <td>83.09</td>
      <td>83.72</td>
      <td>83.20</td>
      <td>83.04</td>
    </tr>
    <tr>
      <th>Wright High School</th>
      <td>83.26</td>
      <td>84.01</td>
      <td>83.84</td>
      <td>83.64</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Avg Reading Pass Rates by School and Grade
students_grouped_by_grade = student_df.groupby(["school","grade"], as_index=False)
avg_reading_score_by_grade = students_grouped_by_grade["reading_score"].mean()
avg_reading_score_by_grade
reading_table = avg_reading_score_by_grade.pivot(index="school", columns="grade", values= "reading_score")
reading_table = reading_table[["9th",
                        "10th",
                        "11th",
                        "12th"]]
reading_table.round(2)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>grade</th>
      <th>9th</th>
      <th>10th</th>
      <th>11th</th>
      <th>12th</th>
    </tr>
    <tr>
      <th>school</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Bailey High School</th>
      <td>81.30</td>
      <td>80.91</td>
      <td>80.95</td>
      <td>80.91</td>
    </tr>
    <tr>
      <th>Cabrera High School</th>
      <td>83.68</td>
      <td>84.25</td>
      <td>83.79</td>
      <td>84.29</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>81.20</td>
      <td>81.41</td>
      <td>80.64</td>
      <td>81.38</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>80.63</td>
      <td>81.26</td>
      <td>80.40</td>
      <td>80.66</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>83.37</td>
      <td>83.71</td>
      <td>84.29</td>
      <td>84.01</td>
    </tr>
    <tr>
      <th>Hernandez High School</th>
      <td>80.87</td>
      <td>80.66</td>
      <td>81.40</td>
      <td>80.86</td>
    </tr>
    <tr>
      <th>Holden High School</th>
      <td>83.68</td>
      <td>83.32</td>
      <td>83.82</td>
      <td>84.70</td>
    </tr>
    <tr>
      <th>Huang High School</th>
      <td>81.29</td>
      <td>81.51</td>
      <td>81.42</td>
      <td>80.31</td>
    </tr>
    <tr>
      <th>Johnson High School</th>
      <td>81.26</td>
      <td>80.77</td>
      <td>80.62</td>
      <td>81.23</td>
    </tr>
    <tr>
      <th>Pena High School</th>
      <td>83.81</td>
      <td>83.61</td>
      <td>84.34</td>
      <td>84.59</td>
    </tr>
    <tr>
      <th>Rodriguez High School</th>
      <td>80.99</td>
      <td>80.63</td>
      <td>80.86</td>
      <td>80.38</td>
    </tr>
    <tr>
      <th>Shelton High School</th>
      <td>84.12</td>
      <td>83.44</td>
      <td>84.37</td>
      <td>82.78</td>
    </tr>
    <tr>
      <th>Thomas High School</th>
      <td>83.73</td>
      <td>84.25</td>
      <td>83.59</td>
      <td>83.83</td>
    </tr>
    <tr>
      <th>Wilson High School</th>
      <td>83.94</td>
      <td>84.02</td>
      <td>83.76</td>
      <td>84.32</td>
    </tr>
    <tr>
      <th>Wright High School</th>
      <td>83.83</td>
      <td>83.81</td>
      <td>84.16</td>
      <td>84.07</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Scores by School Spending
# Create a table that breaks down school performances based on average Spending Ranges (Per Student). Use 4 reasonable bins to group school spending. Include in the table each of the following:
# Average Math Score
# Average Reading Score
# % Passing Math
# % Passing Reading
# Overall Passing Rate (Average of the above two)

#bin schools based on per pupil spending
bin_names = ['<$597.25', '$597.25 to $616.50', '$616.50 to $635.75', '$635.75 to $655']

copy_student_df = student_df
copy_school_df = school_df

#array([ 577.923,  597.25 ,  616.5  ,  635.75 ,  655.   ]))

Scores_by_pp_spending_df = pd.DataFrame({})
copy_school_df["Per Pupil Spending"] = pd.cut(school_df["Per Student Budget"], 4, labels=bin_names)
copy_school_df

merged_df2 = copy_student_df.merge(copy_school_df[['name','Per Pupil Spending']], left_on = 'school',right_on = 'name')
grouped_students_by_spending = merged_df2.groupby(["Per Pupil Spending"])

Scores_by_pp_spending_df["Average Reading Score"] = grouped_students_by_spending["reading_score"].mean()

SS_passing_reading_pp_spending = merged_df2.loc[merged_df2["reading_score"] > 70]
grouped_Ss_passing_reading_pp_spending = SS_passing_reading_pp_spending.groupby(["Per Pupil Spending"])
Scores_by_pp_spending_df["% passing reading"] = grouped_Ss_passing_reading_pp_spending["Student ID"].count()/grouped_students_by_spending["Student ID"].count()*100

Scores_by_pp_spending_df["Average Math Score"] = grouped_students_by_spending["math_score"].mean()

SS_passing_math_pp_spending = merged_df2.loc[merged_df2["math_score"] > 70]
grouped_Ss_passing_math_pp_spending = SS_passing_math_pp_spending.groupby(["Per Pupil Spending"])
Scores_by_pp_spending_df["% passing math"] = grouped_Ss_passing_math_pp_spending["Student ID"].count()/grouped_students_by_spending["Student ID"].count()*100

Scores_by_pp_spending_df["Overall Pass Rate"] = (Scores_by_pp_spending_df["% passing math"]+Scores_by_pp_spending_df["% passing reading"])/2

Scores_by_pp_spending_df["Average Math Score"] = Scores_by_pp_spending_df["Average Math Score"].map("{:,.2f}".format)
Scores_by_pp_spending_df["Average Reading Score"] = Scores_by_pp_spending_df["Average Reading Score"].map("{:,.2f}".format)
Scores_by_pp_spending_df["% passing math"] = Scores_by_pp_spending_df["% passing math"].map("{0:,.2f}%".format)
Scores_by_pp_spending_df["% passing reading"] = Scores_by_pp_spending_df["% passing reading"].map("{0:,.2f}%".format)
Scores_by_pp_spending_df["Overall Pass Rate"] = Scores_by_pp_spending_df["Overall Pass Rate"].map("{0:,.2f}%".format)

Scores_by_pp_spending_df

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Average Reading Score</th>
      <th>% passing reading</th>
      <th>Average Math Score</th>
      <th>% passing math</th>
      <th>Overall Pass Rate</th>
    </tr>
    <tr>
      <th>Per Pupil Spending</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt;$597.25</th>
      <td>83.96</td>
      <td>93.45%</td>
      <td>83.36</td>
      <td>90.33%</td>
      <td>91.89%</td>
    </tr>
    <tr>
      <th>$597.25 to $616.50</th>
      <td>83.84</td>
      <td>92.47%</td>
      <td>83.53</td>
      <td>90.53%</td>
      <td>91.50%</td>
    </tr>
    <tr>
      <th>$616.50 to $635.75</th>
      <td>81.67</td>
      <td>82.51%</td>
      <td>78.48</td>
      <td>70.34%</td>
      <td>76.43%</td>
    </tr>
    <tr>
      <th>$635.75 to $655</th>
      <td>81.15</td>
      <td>79.18%</td>
      <td>77.42</td>
      <td>66.03%</td>
      <td>72.60%</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Scores by School Size

# Repeat the above breakdown, 
#but this time group schools based on a reasonable approximation of school size (Small, Medium, Large).

#bin schools based on size
sizes = ['Small', 'Medium', 'Large']

Scores_by_size = pd.DataFrame({})
copy_school_df["Size"] = pd.cut(school_df["size"], 3, labels=sizes)
copy_school_df

merged_df3 = copy_student_df.merge(copy_school_df[['name','Size']], left_on = 'school',right_on = 'name')
grouped_students_by_size = merged_df3.groupby(["Size"])

Scores_by_size["Average Reading Score"] = grouped_students_by_size["reading_score"].mean()

SS_passing_reading_by_size = merged_df3.loc[merged_df3["reading_score"] > 70]
grouped_Ss_passing_reading_by_size = SS_passing_reading_by_size.groupby(["Size"])
Scores_by_size["% passing reading"] = grouped_Ss_passing_reading_by_size["Student ID"].count()/grouped_students_by_size["Student ID"].count()*100

Scores_by_size["Average Math Score"] = grouped_students_by_size["math_score"].mean()

SS_passing_math_by_size = merged_df3.loc[merged_df3["math_score"] > 70]
grouped_Ss_passing_math_by_size = SS_passing_math_by_size.groupby(["Size"])
Scores_by_size["% passing math"] = grouped_Ss_passing_math_by_size["Student ID"].count()/grouped_students_by_size["Student ID"].count()*100

Scores_by_size["Overall Pass Rate"] = (Scores_by_size["% passing math"]+Scores_by_size["% passing reading"])/2

Scores_by_size["Average Math Score"] = Scores_by_size["Average Math Score"].map("{:,.2f}".format)
Scores_by_size["Average Reading Score"] = Scores_by_size["Average Reading Score"].map("{:,.2f}".format)
Scores_by_size["% passing math"] = Scores_by_size["% passing math"].map("{0:,.2f}%".format)
Scores_by_size["% passing reading"] = Scores_by_size["% passing reading"].map("{0:,.2f}%".format)
Scores_by_size["Overall Pass Rate"] = Scores_by_size["Overall Pass Rate"].map("{0:,.2f}%".format)

Scores_by_size
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Average Reading Score</th>
      <th>% passing reading</th>
      <th>Average Math Score</th>
      <th>% passing math</th>
      <th>Overall Pass Rate</th>
    </tr>
    <tr>
      <th>Size</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Small</th>
      <td>83.88</td>
      <td>93.13%</td>
      <td>83.44</td>
      <td>90.13%</td>
      <td>91.63%</td>
    </tr>
    <tr>
      <th>Medium</th>
      <td>81.65</td>
      <td>81.41%</td>
      <td>78.16</td>
      <td>69.84%</td>
      <td>75.62%</td>
    </tr>
    <tr>
      <th>Large</th>
      <td>80.93</td>
      <td>78.42%</td>
      <td>77.07</td>
      <td>64.34%</td>
      <td>71.38%</td>
    </tr>
  </tbody>
</table>
</div>




```python


Scores_by_school_type = pd.DataFrame({})

merged_df4 = copy_student_df.merge(copy_school_df[['name','type']], left_on = 'school',right_on = 'name')
grouped_students_by_type = merged_df4.groupby(["type"])

Scores_by_school_type["Average Reading Score"] = grouped_students_by_type["reading_score"].mean()

SS_passing_reading_by_type = merged_df4.loc[merged_df4["reading_score"] > 70]
grouped_Ss_passing_reading_by_type = SS_passing_reading_by_type.groupby(["type"])
Scores_by_school_type["% passing reading"] = grouped_Ss_passing_reading_by_type["Student ID"].count()/grouped_students_by_type["Student ID"].count()*100

Scores_by_school_type["Average Math Score"] = grouped_students_by_type["math_score"].mean()

SS_passing_math_by_type = merged_df4.loc[merged_df4["math_score"] > 70]
grouped_Ss_passing_math_by_type = SS_passing_math_by_type.groupby(["type"])
Scores_by_school_type["% passing math"] = grouped_Ss_passing_math_by_type["Student ID"].count()/grouped_students_by_type["Student ID"].count()*100


Scores_by_school_type["Overall Pass Rate"] = (Scores_by_school_type["% passing math"]+Scores_by_school_type["% passing reading"])/2

Scores_by_school_type["Average Math Score"] = Scores_by_school_type["Average Math Score"].map("{:,.2f}".format)
Scores_by_school_type["Average Reading Score"] = Scores_by_school_type["Average Reading Score"].map("{:,.2f}".format)
Scores_by_school_type["% passing math"] = Scores_by_school_type["% passing math"].map("{0:,.2f}%".format)
Scores_by_school_type["% passing reading"] = Scores_by_school_type["% passing reading"].map("{0:,.2f}%".format)
Scores_by_school_type["Overall Pass Rate"] = Scores_by_school_type["Overall Pass Rate"].map("{0:,.2f}%".format)

Scores_by_school_type

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Average Reading Score</th>
      <th>% passing reading</th>
      <th>Average Math Score</th>
      <th>% passing math</th>
      <th>Overall Pass Rate</th>
    </tr>
    <tr>
      <th>type</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Charter</th>
      <td>83.90</td>
      <td>93.15%</td>
      <td>83.41</td>
      <td>90.28%</td>
      <td>91.72%</td>
    </tr>
    <tr>
      <th>District</th>
      <td>80.96</td>
      <td>78.37%</td>
      <td>76.99</td>
      <td>64.31%</td>
      <td>71.34%</td>
    </tr>
  </tbody>
</table>
</div>


