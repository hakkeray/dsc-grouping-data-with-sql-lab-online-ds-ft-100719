{
 "cells": [
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "# Grouping Data with SQL - Lab"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Introduction\n",
    "\n",
    "In this lab, you'll query data from a table populated with Babe Ruth's career hitting statistics.  Then you'll use aggregate functions to pull interesting information from the table that basic queries cannot track. "
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Objectives"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "* Describe the relationship between aggregate functions and `GROUP BY` statements\n",
    "* Use `Group By` statements in SQL to apply aggregate functions like: `COUNT`, `MAX`, `MIN`, and `SUM`\n",
    "* Create an alias in a SQL query\n",
    "* Use the `HAVING` clause to compare different aggregates\n",
    "* Compare the difference between the `WHERE` and `HAVING` clause"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Babe Ruth - Career Hitting Statistics\n",
    "\n",
    "\n",
    "We will query from the `babe_ruth_stats` table featured below.\n",
    "\n",
    "year|team |league|doubles|triples|hits|HR|games|runs|RBI|at_bats|BB |SB|SO|AVG\n",
    "----|-----|------|-------|-------|----|--|-----|----|---|-------|---|--|--|------\n",
    "1914|\"BOS\"|\"AL\"  |1      |0      |2   |0 |5    |1   |2  |10     |0  |0 |4 |0.2\n",
    "1915|\"BOS\"|\"AL\"  |10     |1      |29  |4 |42   |16  |21 |92     |9  |0 |23|0.315\n",
    "1916|\"BOS\"|\"AL\"  |5      |3      |37  |3 |67   |18  |15 |136    |10 |0 |23|0.272\n",
    "1917|\"BOS\"|\"AL\"  |6      |3      |40  |2 |52   |14  |12 |123    |12 |0 |18|0.325\n",
    "1918|\"BOS\"|\"AL\"  |26     |11     |95  |11|95   |50  |66 |317    |58 |6 |58|0.3\n",
    "1919|\"BOS\"|\"AL\"  |34     |12     |139 |29|130  |103 |114|432    |101|7 |58|0.322\n",
    "1920|\"NY\" |\"AL\"  |36     |9      |172 |54|142  |158 |137|458    |150|14|80|0.376\n",
    "1921|\"NY\" |\"AL\"  |44     |16     |204 |59|152  |177 |171|540    |145|17|81|0.378\n",
    "1922|\"NY\" |\"AL\"  |24     |8      |128 |35|110  |94  |99 |406    |84 |2 |80|0.315\n",
    "1923|\"NY\" |\"AL\"  |45     |13     |205 |41|152  |151 |131|522    |170|17|93|0.393\n",
    "1924|\"NY\" |\"AL\"  |39     |7      |200 |46|153  |143 |121|529    |142|9 |81|0.378\n",
    "1925|\"NY\" |\"AL\"  |12     |2      |104 |25|98   |61  |66 |359    |59 |2 |68|0.29\n",
    "1926|\"NY\" |\"AL\"  |30     |5      |184 |47|152  |139 |146|495    |144|11|76|0.372\n",
    "1927|\"NY\" |\"AL\"  |29     |8      |192 |60|151  |158 |164|540    |137|7 |89|0.356\n",
    "1928|\"NY\" |\"AL\"  |29     |8      |173 |54|154  |163 |142|536    |137|4 |87|0.323\n",
    "1929|\"NY\" |\"AL\"  |26     |6      |172 |46|135  |121 |154|499    |72 |5 |60|0.345\n",
    "1930|\"NY\" |\"AL\"  |28     |9      |186 |49|145  |150 |153|518    |136|10|61|0.359\n",
    "1931|\"NY\" |\"AL\"  |31     |3      |199 |46|145  |149 |163|534    |128|5 |51|0.373\n",
    "1932|\"NY\" |\"AL\"  |13     |5      |156 |41|133  |120 |137|457    |130|2 |62|0.341\n",
    "1933|\"NY\" |\"AL\"  |21     |3      |138 |34|137  |97  |103|459    |114|4 |90|0.301\n",
    "1934|\"NY\" |\"AL\"  |17     |4      |105 |22|125  |78  |84 |365    |104|1 |63|0.288\n",
    "1935|\"BOS\"|\"NL\"  |0      |0      |13  |6 |28   |13  |12 |72     |20 |0 |24|0.181"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Connect to the Database"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Import sqlite3 and pandas. Then, connect to the database and instantiate a cursor."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 34,
   "metadata": {},
   "outputs": [],
   "source": [
    "import sqlite3\n",
    "import pandas as pd\n",
    "conn = sqlite3.Connection('babe_ruth.db')\n",
    "cur = conn.cursor()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Total Seasons\n",
    "Return the total number of years that Babe Ruth played professional baseball."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 35,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>COUNT(year)</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <td>0</td>\n",
       "      <td>22</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "   COUNT(year)\n",
       "0           22"
      ]
     },
     "execution_count": 35,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "cur.execute(\"\"\"SELECT COUNT(year) FROM babe_ruth_stats;\"\"\")\n",
    "df = pd.DataFrame(cur.fetchall())\n",
    "df.columns = [x[0] for x in cur.description]\n",
    "df.head()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Seasons with NY\n",
    "Return the total number of years Babe Ruth played with the NY Yankees."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 36,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>COUNT(year)</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <td>0</td>\n",
       "      <td>15</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "   COUNT(year)\n",
       "0           15"
      ]
     },
     "execution_count": 36,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "cur.execute(\"\"\"SELECT COUNT(year) FROM babe_ruth_stats\n",
    "                WHERE team = 'NY';\"\"\")\n",
    "df = pd.DataFrame(cur.fetchall())\n",
    "df.columns = [x[0] for x in cur.description]\n",
    "df.head()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Most HR\n",
    "Select the row with the most HR that Babe Ruth hit in one season"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 37,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>MAX(HR)</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <td>0</td>\n",
       "      <td>60</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "   MAX(HR)\n",
       "0       60"
      ]
     },
     "execution_count": 37,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "cur.execute(\"\"\"SELECT MAX(HR) FROM babe_ruth_stats;\"\"\")\n",
    "df = pd.DataFrame(cur.fetchall())\n",
    "df.columns = [x[0] for x in cur.description]\n",
    "df.head()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Least HR\n",
    "Select the row with the least number of HR hit in one season."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 38,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>MIN(HR)</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "   MIN(HR)\n",
       "0        0"
      ]
     },
     "execution_count": 38,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "cur.execute(\"\"\"SELECT MIN(HR) FROM babe_ruth_stats;\"\"\")\n",
    "df = pd.DataFrame(cur.fetchall())\n",
    "df.columns = [x[0] for x in cur.description]\n",
    "df.head()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Total HR\n",
    "Return the total number of `HR` hit by Babe Ruth during his career"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 39,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>SUM(HR)</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <td>0</td>\n",
       "      <td>714</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "   SUM(HR)\n",
       "0      714"
      ]
     },
     "execution_count": 39,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "cur.execute(\"\"\"SELECT SUM(HR) FROM babe_ruth_stats;\"\"\")\n",
    "df = pd.DataFrame(cur.fetchall())\n",
    "df.columns = [x[0] for x in cur.description]\n",
    "df.head()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "##  Five Worst HR Seasons With at Least 100 Games Played\n",
    "Above you saw that Babe Ruth hit 0 home runs in his first year when he played only five games.  To avoid this and other extreme  outliers, first filter the data to include only those years in which Ruth played in at least 100 games. Then, select all of the columns for the 5 worst seasons, in terms of the number of home runs, where he played over 100 games."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 45,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>id</th>\n",
       "      <th>year</th>\n",
       "      <th>team</th>\n",
       "      <th>league</th>\n",
       "      <th>doubles</th>\n",
       "      <th>triples</th>\n",
       "      <th>hits</th>\n",
       "      <th>HR</th>\n",
       "      <th>games</th>\n",
       "      <th>runs</th>\n",
       "      <th>RBI</th>\n",
       "      <th>at_bats</th>\n",
       "      <th>BB</th>\n",
       "      <th>SB</th>\n",
       "      <th>SO</th>\n",
       "      <th>AVG</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <td>0</td>\n",
       "      <td>21</td>\n",
       "      <td>1934</td>\n",
       "      <td>NY</td>\n",
       "      <td>AL</td>\n",
       "      <td>17</td>\n",
       "      <td>4</td>\n",
       "      <td>105</td>\n",
       "      <td>22</td>\n",
       "      <td>125</td>\n",
       "      <td>78</td>\n",
       "      <td>84</td>\n",
       "      <td>365</td>\n",
       "      <td>104</td>\n",
       "      <td>1</td>\n",
       "      <td>63</td>\n",
       "      <td>0.288</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <td>1</td>\n",
       "      <td>6</td>\n",
       "      <td>1919</td>\n",
       "      <td>BOS</td>\n",
       "      <td>AL</td>\n",
       "      <td>34</td>\n",
       "      <td>12</td>\n",
       "      <td>139</td>\n",
       "      <td>29</td>\n",
       "      <td>130</td>\n",
       "      <td>103</td>\n",
       "      <td>114</td>\n",
       "      <td>432</td>\n",
       "      <td>101</td>\n",
       "      <td>7</td>\n",
       "      <td>58</td>\n",
       "      <td>0.322</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <td>2</td>\n",
       "      <td>20</td>\n",
       "      <td>1933</td>\n",
       "      <td>NY</td>\n",
       "      <td>AL</td>\n",
       "      <td>21</td>\n",
       "      <td>3</td>\n",
       "      <td>138</td>\n",
       "      <td>34</td>\n",
       "      <td>137</td>\n",
       "      <td>97</td>\n",
       "      <td>103</td>\n",
       "      <td>459</td>\n",
       "      <td>114</td>\n",
       "      <td>4</td>\n",
       "      <td>90</td>\n",
       "      <td>0.301</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <td>3</td>\n",
       "      <td>9</td>\n",
       "      <td>1922</td>\n",
       "      <td>NY</td>\n",
       "      <td>AL</td>\n",
       "      <td>24</td>\n",
       "      <td>8</td>\n",
       "      <td>128</td>\n",
       "      <td>35</td>\n",
       "      <td>110</td>\n",
       "      <td>94</td>\n",
       "      <td>99</td>\n",
       "      <td>406</td>\n",
       "      <td>84</td>\n",
       "      <td>2</td>\n",
       "      <td>80</td>\n",
       "      <td>0.315</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <td>4</td>\n",
       "      <td>10</td>\n",
       "      <td>1923</td>\n",
       "      <td>NY</td>\n",
       "      <td>AL</td>\n",
       "      <td>45</td>\n",
       "      <td>13</td>\n",
       "      <td>205</td>\n",
       "      <td>41</td>\n",
       "      <td>152</td>\n",
       "      <td>151</td>\n",
       "      <td>131</td>\n",
       "      <td>522</td>\n",
       "      <td>170</td>\n",
       "      <td>17</td>\n",
       "      <td>93</td>\n",
       "      <td>0.393</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "   id  year team league  doubles  triples  hits  HR  games  runs  RBI  \\\n",
       "0  21  1934   NY     AL       17        4   105  22    125    78   84   \n",
       "1   6  1919  BOS     AL       34       12   139  29    130   103  114   \n",
       "2  20  1933   NY     AL       21        3   138  34    137    97  103   \n",
       "3   9  1922   NY     AL       24        8   128  35    110    94   99   \n",
       "4  10  1923   NY     AL       45       13   205  41    152   151  131   \n",
       "\n",
       "   at_bats   BB  SB  SO    AVG  \n",
       "0      365  104   1  63  0.288  \n",
       "1      432  101   7  58  0.322  \n",
       "2      459  114   4  90  0.301  \n",
       "3      406   84   2  80  0.315  \n",
       "4      522  170  17  93  0.393  "
      ]
     },
     "execution_count": 45,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "cur.execute(\"\"\"SELECT * FROM babe_ruth_stats\n",
    "                WHERE games >= 100\n",
    "                GROUP BY 1\n",
    "                HAVING min(HR)\n",
    "                ORDER BY HR ASC\n",
    "                LIMIT 5;\"\"\")\n",
    "df = pd.DataFrame(cur.fetchall())\n",
    "df.columns = [x[0] for x in cur.description]\n",
    "df.head()\n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Average Batting Average\n",
    "Select the average, `AVG`, of Ruth's batting averages.  The header of the result would be `AVG(AVG)` which is quite confusing, so provide an alias of `career_average`."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 47,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>career_average</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <td>0</td>\n",
       "      <td>0.322864</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "   career_average\n",
       "0        0.322864"
      ]
     },
     "execution_count": 47,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "cur.execute(\"\"\"SELECT AVG(AVG) AS career_average\n",
    "                FROM babe_ruth_stats;\"\"\")\n",
    "df = pd.DataFrame(cur.fetchall())\n",
    "df.columns = [x[0] for x in cur.description]\n",
    "df.head()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Total Years and Hits Per Team\n",
    "Select the total number of years played (AS num_years) and total hits (AS total_hits) Babe Ruth had for each team he played for."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 61,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>team</th>\n",
       "      <th>num_years</th>\n",
       "      <th>total_hits</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <td>0</td>\n",
       "      <td>BOS</td>\n",
       "      <td>7</td>\n",
       "      <td>355</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <td>1</td>\n",
       "      <td>NY</td>\n",
       "      <td>15</td>\n",
       "      <td>2518</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "  team  num_years  total_hits\n",
       "0  BOS          7         355\n",
       "1   NY         15        2518"
      ]
     },
     "execution_count": 61,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "cur.execute(\"\"\"SELECT team, count(year) AS num_years,\n",
    "                SUM(hits) AS total_hits\n",
    "                FROM babe_ruth_stats\n",
    "                GROUP BY team;\"\"\")\n",
    "df = pd.DataFrame(cur.fetchall())\n",
    "df.columns = [x[0] for x in cur.description]\n",
    "df.head()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Number of Years with Over 300 Times On Base\n",
    "We want to know the years in which Ruth successfully reached base over 300 times.  We need to add `hits` and `BB` to calculate how many times Ruth reached base.  Simply add the two columns together (ie: `SELECT [columnName] + [columnName] AS ...`) and give this value an alias of `on_base`.  Select the `year` and `on_base` for only those years with an `on_base` over 300.  "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 62,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>year</th>\n",
       "      <th>on_base</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <td>0</td>\n",
       "      <td>1920</td>\n",
       "      <td>322</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <td>1</td>\n",
       "      <td>1921</td>\n",
       "      <td>349</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <td>2</td>\n",
       "      <td>1923</td>\n",
       "      <td>375</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <td>3</td>\n",
       "      <td>1924</td>\n",
       "      <td>342</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <td>4</td>\n",
       "      <td>1926</td>\n",
       "      <td>328</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "   year  on_base\n",
       "0  1920      322\n",
       "1  1921      349\n",
       "2  1923      375\n",
       "3  1924      342\n",
       "4  1926      328"
      ]
     },
     "execution_count": 62,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "cur.execute(\"\"\"SELECT year,\n",
    "                hits + BB AS on_base\n",
    "                FROM babe_ruth_stats\n",
    "                GROUP BY year\n",
    "                HAVING on_base > 300;\"\"\")\n",
    "df = pd.DataFrame(cur.fetchall())\n",
    "df.columns = [x[0] for x in cur.description]\n",
    "df.head()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Summary"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Well done! In this lab, you continued to add complexity to SQL statements, which included using some aggregate functions, the `GROUP BY` statement, and the `HAVING` statement. You wrote queries that showed Babe Ruth's total years and home runs per team as well as selected only years that met a minimum value of our calculated on base attribute. "
   ]
  }
 ],
 "metadata": {
  "kernelspec": {
   "display_name": "Python 3",
   "language": "python",
   "name": "python3"
  },
  "language_info": {
   "codemirror_mode": {
    "name": "ipython",
    "version": 3
   },
   "file_extension": ".py",
   "mimetype": "text/x-python",
   "name": "python",
   "nbconvert_exporter": "python",
   "pygments_lexer": "ipython3",
   "version": "3.6.9"
  },
  "toc": {
   "base_numbering": 1,
   "nav_menu": {},
   "number_sections": true,
   "sideBar": true,
   "skip_h1_title": false,
   "title_cell": "Table of Contents",
   "title_sidebar": "Contents",
   "toc_cell": false,
   "toc_position": {},
   "toc_section_display": true,
   "toc_window_display": false
  },
  "varInspector": {
   "cols": {
    "lenName": 16,
    "lenType": 16,
    "lenVar": 40
   },
   "kernels_config": {
    "python": {
     "delete_cmd_postfix": "",
     "delete_cmd_prefix": "del ",
     "library": "var_list.py",
     "varRefreshCmd": "print(var_dic_list())"
    },
    "r": {
     "delete_cmd_postfix": ") ",
     "delete_cmd_prefix": "rm(",
     "library": "var_list.r",
     "varRefreshCmd": "cat(var_dic_list()) "
    }
   },
   "types_to_exclude": [
    "module",
    "function",
    "builtin_function_or_method",
    "instance",
    "_Feature"
   ],
   "window_display": false
  }
 },
 "nbformat": 4,
 "nbformat_minor": 2
}
