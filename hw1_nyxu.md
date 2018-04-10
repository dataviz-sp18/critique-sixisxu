MACS 40700: Assignment 1
================

Part 1: Visualization critique
==============================

> I will be critiquing the visualization from [the New York Times](https://www.nytimes.com/interactive/2018/03/19/upshot/race-class-white-and-black-men.html). There are 5 graphs in total in this article (and 5 more for "other findings") and some of them are not static, thus I am not including all of them here. I will cite them by their order in the source page, so please refer to the page while the critique goes on.

I picked this visualization because I really like it on the first sight. Focusing on the issue of racial difference in upward/downward income mobility, the visualization in the article gives an intuitive demostration. And as the article continues, the reasons for the phenomenon are slowly revealed with more graphs and analytics. I think the whole article tells a good story, yet I do find something to critize over about the data visualizations.

Truthful
--------

The truthfulness of this visualization seems to be well ensured by [the study it cites](http://www.equality-of-opportunity.org/), which claims that their data are from American Census and federal income tax returns with a detailed description. The numbers, scales, and descriptions in the graphs all seem honest, so I would say the visualization is truthful.

The only thing I want to complain about is the animated graphs. They are very intuitive and easy to understand, but they could use some more background information. For example, in the first animated graph, the description says it shows "the lives of 10,000 boys who are from rich families" and further claims that "most white boys raised in wealthy families will stay rich but black boys raised in similarly rich households will not". The benefit is that audience would be impressed by the importance of the main issue, however, they might ignore factors other than race/wealth that might influence these boys' future wealthiness. And I suppose the number 10,000 is for better demonstration, but I would like to know how numbers or observations in this graph are picked. I could not find the original graph from Raj Chetty's study either, which, I think, could be because these graphs are emphasizing the race factor.

Functional
----------

The functionality is well described by my first impression as a reader: the article tells a good story, and the graphs serve as good supporting tools. The 5 graphs in the main article formed a logic chain: 1) In general, Black men raised in similarly rich households as white men will end up poorer (graph 1); 2) Some argues the reason is differences in cognitive ability. However, while the gap exists for both black boys and girls, the income gap only persist between men but not women (graph 2); 3) Some says the reason lies in surrounding neighborhoods, and researchers find that the racial bias and the presence of fathers in the neighborhoods might be influential (graph 3); 4) There is something unique about the obstacles black males face. Most poor black boys will remain poor as adults, while white boys raised in poor families fare far better (graph 4); 5) Boys, across races, are more sensitive than girls to racial discrimination. (graph 5). All the graphs did a great job at conveying necessary information without being too detailed.

As graphs for usual audience without acdemic background, the graphs are very understandable and well-explained, with some texts on the graphs. Thus I think the visualization is functional.

Beautiful
---------

I think this visualization is very beautiful. It has a clean, simple style, and has blue and yellow as two main colors, which are differentiable but easy on eyes. The color palette works expecially well in the animated graph.

Since "feeling of beauty" is subjective, I still managed to find some aspects of this visulization that I think could be better. For graph 2, I persoanlly think the data points are too big, making it hard to see the corresponding x/y values. Contrarily, this is not a problem in the case of graph 5 since the graph is larger while the size of the data points remains the same. For graph 3, the texts on the bars are a little confusing. I think legends on the side and numbers on the bar would look better. And it can also be a "stacked" bar plot. Another thing I could nitpick about is the size of graphs. If we expand the page to full screen, the first graph seems much wider than the text width. In generaul, I still think the visualization is beautiful.

Insightful
----------

Speaking of insightful, this visualization gives both "a-ha" moments and a gradual and deliberate process of exploration of the information. The combination of these two kinds of insight makes sense to me considering the story they are trying to tell. At first, the animated graph will give you an "a-ha" moment (it could also be regarded as "knowledge-building insight" if you consider the time waiting for the results), showing how racial difference hugely impacted downward mobility. Nevertheless, the article doesn't want you to think that's the simple reason. They then uses 4 more graphs to tell how they crystallize the data and find out the potential reasons. Each graph sends a clear message, and together they build up to the final image the article is trying to tell. Thus it's a insightful visualization.

Enlightening
------------

Revisiting the topic, one will find this visualization is very enlightening.

A defining feature of the “American Dream” is upward income mobility: the ideal that children have a higher standard of living than their parents. But this article brings up an alarming fact: black boys — even rich black boys — can hardly assume the realization of "American Dream". Income gaps persisted even when black and white boys grew up in families with the same income, similar family structures, similar education levels and even similar levels of accumulated wealth. And the gap is uniquely large for black males, comparing to other non-white races.

In addition, the article makes an effort to analyze the cause: it's not cognitive ability or test scores, not other individual or household traits, but could be lower levels of racial bias and presence of fathers in the neighborhood. This conclusion offers a possible solution to othe issue, and could influence related policies and the future of African Americans.

The topic of this article is critical to the well-being of a group of people, and it is delivered in such a fun and fancy way. In a nutshell, I think this is a very good example of data visualization and my goal for taking is class is to create something like this.

Part 2: ggplot2 and the grammar of graphics
===========================================

``` r
dat <- read.dta13("educ_00.dta") %>% mutate(idind = as.character(idind))
age <- read.dta13("surveys_pub_01.dta") %>% mutate(idind = as.character(idind)) %>% select(idind, age)

dat <- left_join(dat, age, by = "idind")

educ_val <- list('0'=c('0', 'No school'), 
                 '11'=c('1', '1yr primary'), 
                 '12'=c('2', '2yr primary'), 
                 '13'=c('3', '3yr primary'), 
                 '14'=c('4', '4yr primary'), 
                 '15'=c('5', '5yr primary'), 
                 '16'=c('6', '6yr primary'), 
                 '21'=c('7', '1yr lower middle'), 
                 '22'=c('8', '2yr lower middle'), 
                 '23'=c('9', '3yr lower middle'), 
                 '24'=c('10', '1yr upper middle'), 
                 '25'=c('11', '2yr upper middle'), 
                 '26'=c('12', '3yr upper middle'), 
                 '27'=c('13', '1yr technical'), 
                 '28'=c('14', '2yr technical'), 
                 '29'=c('15', '3yr technical'), 
                 '31'=c('16', '1yr college'), 
                 '32'=c('17', '2yr college'), 
                 '33'=c('18', '3yr college'), 
                 '34'=c('19', '4yr college'), 
                 '35'=c('20', '5yr college'), 
                 '36'=c('21', '6yr college or more'))

dat %>% mutate(educ=NA, educ_num=NA) %>% {.} -> dat
for(i in names(educ_val)) {
  dat %>% mutate(educ = ifelse(as.character(a11)==i,
                               educ_val[[i]][2], 
                              educ),
                 educ_num = ifelse(as.character(a11)==i,
                         educ_val[[i]][1], educ_num)) %>%
                         {.} -> dat
}

dat %>% 
  filter(wave==2011) %>% 
  filter(is.na(a11)==FALSE) %>% 
  mutate(educ_level = 
           ifelse(as.numeric(educ_num)>12, 2, 
                  ifelse((age<=16) & (age-6<=as.numeric(educ_num)), 1, 0))) %>%
  filter(age-as.numeric(educ_num)>=0) %>% 
  select(idind, t2, age, educ_num, educ, educ_level) %>%
  mutate(urban=ifelse(t2==1,"urban","rural")) %>% 
  drop_na() %>% 
  {.} -> educdata

educdata %>% 
  mutate(urban,
         educ_num=as.numeric(educ_num)) %>% 
  ggplot(aes(x=age, y=educ_num, color=urban)) +
  geom_smooth(alpha=0.3, span=0.3, position="identity") +
  scale_y_continuous(breaks = c(0,4,8,12)) +
  scale_x_continuous(breaks = c(0,20,40,60,80,100)) +
  labs(title = "Years of Education by Household Registration Area (China)",
       subtitle = "Conditional Mean Over Age",
       x = "Age",
       y = "Years of Education",
       color = "Household",
       caption = "Source: China Health and Nutrition Survey (2011)")
```

![](hw1_nyxu_files/figure-markdown_github/unnamed-chunk-1-1.png)

``` r
# educdata %>% 
#   mutate(urban,
#          educ_num=as.numeric(educ_num)) %>% 
#   ggplot(aes(x=educ_level, color=urban)) +
#   geom_density()
```

I am trying to show the differences between urban and rural area in China regarding the issue of number of education years. The data is from China Health and Nutrition Survey (CHNS). I chose the year 2011 to observe the phenomenon. (It is only after I started cleaning the data do I realize how messy it is.) In China, the difference between urban and rural areas is the main issue of the society. People living in the urban areas have the access to better health facilities, education resources, and other social benefits comparing to the rural area, thus creating gaps in income, wealth, etc. I here look at the years of education for individuals in these two areas so to observe this issue from one aspect.

After several tryouts, I picked "geom\_smooth": "Smoothed Conditional Means" to demonstrate the relationship I found. There are 3 things I want to include in this graph: The first and most important one is the comparison of urban and rural areas. The 2nd is the relationship between age and number of education, which should stay constant if we reach certain age. However, when this is not the case, the relationship could show the trend of how people value education in the whole country. The 3rd one is the average years of education in general for China. I think "geom\_smooth" is the most suitable graphical form in this case. It is built for continuous X and continuous Y, and it has the pretty line feature so the whole graph is not covered by points. And one could clearly see the difference between urban and rural areas from this graph. And the general trend of secondary education attainment in China (Older people are less educated due to the under-developmenet of the country).

In terms of specific channels, the first choice I made is setting the themem to minimal. This gives out a clean, simple impression for my graphs. Option choices I made for "geom\_smooth" include "position = identity", "alpha = 0.3", and "span = 0.3". For position, identity is the default choice since I don't see the reason to scale or change it. Changing "alpha" makes the confidence interval around the smoothed line more visible, especially in the case when there is overlapping part between two areas. And I set "span" to a small number because I want the smoothed line to stick to the truth, which would be wigglier and less smooth. For ggplot2 options, I made the "aes": "x=age, y=educ\_num, color=urban", which would result in two lines for each household area, and each mapping the relatinship between age and years of education. I made modifications to the break points of x and y axis, so that the highest point of smoothes lines are easier to locate. In the end I changed the titles and labels of this graph so the audience would know what I am plotting.

The data transformations are mostly for cleaning the data, including dropping NA values and unnecessary columns. One thing worth noticing is that I realize in this dataset there are some cases that a person whose age is 0 but has 6 years of education experience. I also filtered out those weird observations by claiming that age should not be smaller than the years of formal education. We ended up with 62,420 observations. And I created a list to tranform original code for "years of education" from the dataset into education years, with "1yr primary" being "1 year of education". This way the year of education is a continuous variable and could be plotted by "geom\_smooth".

In addition, I also did some transformations to create education-related variables other than years of education. This include "educ", a string variable describing the actual education level for each year of education, and "educ\_level", which is a variable I created to say whether the individual received "secondary education", if not, whether they received "compulsory education" in accordance with their age (for example, 7 for 1st year primary school based on Compulsory Education Law of the PRC). "Educ\_level" is a variable that could potentially be useful in showing the education attainment, and in my tryouts I created a kdensity plot for this. The implication is the same as "smooth" plot, where people in rural area show disadvantage in education attainment. Due to the assignment requirement, I omitted the density plot.

To summarize, my final result integrates lines, texts and colors to describe the differences in years of education between urban and rural areas. The graph serves well for showing the gap. Some might argue that the smoothed line might imply the causal inference between x and y variables, but since they are age and years of education, I think it is common knowledge that they are correlated.
