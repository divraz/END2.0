# Dataset Creation

- Download and unzip the data 
```
!wget http://nlp.stanford.edu/~socherr/stanfordSentimentTreebank.zip
!unzip *.zip
```
- Load the sentence phrases and labels as separate pandas dataframes because they are in separate files
```
text_data = pd.read_csv (
    text_file, 
    delimiter = '\t'
    )
label_data = pd.read_csv (
    label_file, 
    delimiter = '|',
    )
```
- Divide the label into 3 labels from a range of 0 to 1 values
  - 0 means negative - less than 0.4
  - 1 means neutral - between 0.4 and 0.6
  - 2 means positive - greater than 0.6
- Merge sentence and labels based on sentence ids
```
df = pd.merge (
    left = text_data,
    right = label_data,
    left_on = 'sentence_index',
    right_on = 'phrase ids'
)
>>> df['sentiment values'].value_counts () 
1.0    6240
0.0    2813
2.0    2802
Name: sentiment values, dtype: int64
```

# Data Augmentation
For text data augmentation is costly, so we have done the augmentation before hand.

## Back Translation
- Translate a sentence to any random language.
- Translate it back to english using google translate
- You will get different sentence structure but the meaning remains intact
- Examples:
```
original: The Rock is destined to be the 21st Century 's new `` Conan '' and that he 's going to make a splash even greater than Arnold Schwarzenegger , Jean-Claud Van Damme or Steven Segal .
translated: The stone is destined to be the new `` conan 'of the 21st century and that he is going to make a splash even bigger than Arnold Schwarzenegger, Jean-Claud Van Damme or Steven Segal. 

original: Take Care of My Cat offers a refreshingly different slice of Asian cinema .
translated: Take care of my cat offers a variety of wishes to refresh the Asian cinema. 

original: Offers a breath of the fresh air of true sophistication .
translated: The actual skill offers that the actual weather is breathing. 

original: It helps that Lil Bow Wow ... tones down his pint-sized gangsta act to play someone who resembles a real kid .
translated: It helps that Lil Bow Wow ... Lower the Word of his Pint-size gang to play someone who is like a real child. 

original: Like most Bond outings in recent years , some of the stunts are so outlandish that they border on being cartoonlike .
translated: Like most bond expressions in recent years, some of the stunts are out of the fact that some of the stunts are cartoonie. 

original: Tender yet lacerating and darkly funny fable .
translated: Gentle still anchor and dark funny narrative. 

original: Singer\/composer Bryan Adams contributes a slew of songs -- a few potential hits , a few more simply intrusive to the story -- but the whole package certainly captures the intended , er , spirit of the piece .
translated: The singer / composer Bryan Adams adds slew songs - some potential hits, a few things that affect the story - but certainly gives the whole package the planned spirit, ER, spirit of the piece. 

original: Morton is a great actress portraying a complex character , but Morvern Callar grows less compelling the farther it meanders from its shocking start .
translated: Morton is a great actress that depicts a complicated character, but MORVERN CALLAR grows less conquest of further meanders from his shocking start. 

original: Gollum 's ` performance ' is incredible !
translated: Gollum's `Performance 'is incredible! 

original: Together , Tok and O orchestrate a buoyant , darkly funny dance of death .
translated: Together, Talk and Ohot, Headed Ambaranance. 

original: If nothing else , this movie introduces a promising , unusual kind of psychological horror .
translated: If nothing else this movie represents a promising, unusual kind of psychological horror. 

original: The values that have held the Enterprise crew together through previous adventures and perils do so again-courage , self-sacrifice and patience under pressure .
translated: The values held by the business staff through previous adventures and dangers do it again, self-sacrifice and patience under pressure. 
```
- Random elements are picked up to do this augmentation
```
1.0    963
0.0    417
2.0    415
Name: labels, dtype: int64
```

## Random Insert
- Download Google word to vec with 300 dimensions
- Choose random 5 words from a sentence
- Switch those words with the synonym from Google word to vec
- Examples:
```
original: The Rock is destined to be the 21st Century 's new `` Conan '' and that he 's going to make a splash even greater than Arnold Schwarzenegger , Jean-Claud Van Damme or Steven Segal .
replaced_words: [[0, 'Schwarzenegger', 'Gov._Arnold_Schwarzenegger'], [1, 'Arnold', 'Giunta_Bertucci_Funeral_Home'], [2, 'splash', 'splashes']]
new sentence: The Rock is destined to be the 21st Century 's new `` Conan '' and that he 's going to make a splashes even greater than Giunta_Bertucci_Funeral_Home Gov._Giunta_Bertucci_Funeral_Home_Schwarzenegger , Jean-Claud Van Damme or Steven Segal .

original: Frailty is n't as gory or explicit .
replaced_words: [[1, 'Frailty', 'Typecasting']]
new sentence: Typecasting is n't as gory or explicit .

original: An average coming-of-age tale elevated by the wholesome twist of a pesky mother interfering during her son 's discovery of his homosexuality .
replaced_words: [[0, 'coming', 'come'], [1, 'twist', 'twists'], [3, 'pesky', 'peskier'], [4, 'elevated', 'Elevated']]
new sentence: An average come-of-age tale Elevated by the wholesome twists of a peskier mother interfering during her son 's discovery of his homosexuality .

original: Discursive but oddly riveting documentary .
replaced_words: [[0, 'documentary', 'documentaries'], [1, 'Discursive', 'Discursive'], [2, 'riveting', 'captivating']]
new sentence: Discursive but oddly captivating documentaries .

original: Spectacular in every sense of the word , even if you don ' t know an Orc from a Uruk-Hai .
replaced_words: [[2, 'Orc', 'Orc_Trading'], [3, 'sense', 'humor_Jena_Elayan']]
new sentence: Spectacular in every humor_Jena_Elayan of the word , even if you don ' t know an Orc_Trading from a Uruk-Hai .

original: Ash Wednesday is not Edward Burns ' best film , but it is a good and ambitious film .
replaced_words: [[0, 'Wednesday', 'Thursday'], [1, 'best', 'finest'], [3, 'Edward', 'William']]
new sentence: Ash Thursday is not William Burns ' finest film , but it is a good and ambitious film .

original: There 's no good answer to that one .
replaced_words: [[0, 'answer', 'answers'], [2, 'good', 'great']]
new sentence: There 's no great answers to that one .

original: Large budget notwithstanding , the movie is such a blip on the year 's radar screen that it 's tempting just to go with it for the ride .
replaced_words: [[0, 'Large', 'large'], [2, 'screen', 'screens'], [4, 'ride', 'rides']]
new sentence: large budget notwithstanding , the movie is such a blip on the year 's radar screens that it 's tempting just to go with it for the rides .

original: After that , it just gets stupid and maudlin .
replaced_words: [[0, 'gets', 'receives'], [2, 'stupid', 'dumb'], [3, 'maudlin', 'mawkish']]
new sentence: After that , it just receives dumb and mawkish .

original: Big Fat Liar is just futile silliness looking to tap into the kiddie sensibilities .
replaced_words: [[0, 'Fat', 'dubbed_El_Gordo'], [2, 'looking', 'look'], [4, 'sensibilities', 'sensibility']]
new sentence: Big dubbed_El_Gordo Liar is just futile silliness look to tap into the kiddie sensibility .

original: In all the annals of the movies , few films have been this odd , inexplicable and unpleasant .
replaced_words: [[0, 'unpleasant', 'unwelcome'], [3, 'inexplicable', 'unexplainable']]
new sentence: In all the annals of the movies , few films have been this odd , unexplainable and unwelcome .
```
- Random elements are choosen for this augmentation
```
1.0    1296
0.0     553
2.0     522
Name: labels, dtype: int64
```

## Random Deletion
- We select random words from a sentence and based on probability delete them.
- Examples:
```
original: The Rock is destined to be the 21st Century 's new `` Conan '' and that he 's going to make a splash even greater than Arnold Schwarzenegger , Jean-Claud Van Damme or Steven Segal .
new: The Rock the Century 's new `` Conan '' and he 's going to make a splash even than Arnold Schwarzenegger Jean-Claud Damme or Steven Segal .

original: Frailty is n't as gory or explicit .
new: Frailty is n't as gory explicit .

original: An average coming-of-age tale elevated by the wholesome twist of a pesky mother interfering during her son 's discovery of his homosexuality .
new: An tale elevated the wholesome twist a pesky mother interfering during son 's discovery of his .

original: Discursive but oddly riveting documentary .
new: Discursive but oddly riveting

original: Spectacular in every sense of the word , even if you don ' t know an Orc from a Uruk-Hai .
new: Spectacular every of the word , if you ' t Orc from a Uruk-Hai

original: Ash Wednesday is not Edward Burns ' best film , but it is a good and ambitious film .
new: Wednesday is not Edward Burns ' best film , but it is ambitious

original: There 's no good answer to that one .
new: 's good answer one .

original: Large budget notwithstanding , the movie is such a blip on the year 's radar screen that it 's tempting just to go with it for the ride .
new: Large budget notwithstanding , the movie is such the year 's radar it tempting just to go it the

original: After that , it just gets stupid and maudlin .
new: After , it gets stupid .

original: Big Fat Liar is just futile silliness looking to tap into the kiddie sensibilities .
new: Big Fat Liar is just futile silliness looking to into kiddie

original: In all the annals of the movies , few films have been this odd , inexplicable and unpleasant .
new: In the annals of movies , have odd , inexplicable and .

original: A thriller without a lot of thrills .
new: A thriller without a lot of .
```
- Augmentation is applied for all the elements.
```
1.0    6240
0.0    2813
2.0    2802
Name: labels, dtype: int64
```

## Random swap
- We select randomly 2 words in the sentence
- We swap position of those 2 words.
- Examples:
```0
original: The Rock is destined to be the 21st Century 's new `` Conan '' and that he 's going to make a splash even greater than Arnold Schwarzenegger , Jean-Claud Van Damme or Steven Segal .
swapped words: [['going', '21st'], ['.', 'Schwarzenegger']]
replaced_words: The Rock is destined to be the going Century 's new `` Conan '' and that he 's 21st to make a splash even greater than Arnold . , Jean-Claud Van Damme or Steven Segal Schwarzenegger

original: Frailty is n't as gory or explicit .
swapped words: [['gory', "n't"], ['or', 'as']]
replaced_words: Frailty is gory or n't as explicit .

original: An average coming-of-age tale elevated by the wholesome twist of a pesky mother interfering during her son 's discovery of his homosexuality .
swapped words: [['.', 'pesky'], ['her', 'the']]
replaced_words: An average coming-of-age tale elevated by her wholesome twist of a . mother interfering during the son 's discovery of his homosexuality pesky

original: Discursive but oddly riveting documentary .
swapped words: [['oddly', '.'], ['.', 'documentary']]
replaced_words: Discursive but documentary riveting . oddly

original: Spectacular in every sense of the word , even if you don ' t know an Orc from a Uruk-Hai .
swapped words: [[',', 't'], ['know', 'Orc']]
replaced_words: Spectacular in every sense of the word t even if you don ' , Orc an know from a Uruk-Hai .

original: Ash Wednesday is not Edward Burns ' best film , but it is a good and ambitious film .
swapped words: [['film', 'Wednesday'], ['Ash', 'a']]
replaced_words: a film is not Edward Burns ' best film , but it is Ash good and ambitious Wednesday .

original: There 's no good answer to that one .
swapped words: [["'s", 'that'], ['answer', 'to']]
replaced_words: There that no good to answer 's one .

original: Large budget notwithstanding , the movie is such a blip on the year 's radar screen that it 's tempting just to go with it for the ride .
swapped words: [['screen', 'movie'], ['.', 'radar']]
replaced_words: Large budget notwithstanding , the screen is such a blip on the year 's . movie that it 's tempting just to go with it for the ride radar

original: After that , it just gets stupid and maudlin .
swapped words: [['stupid', 'it'], ['.', 'stupid']]
replaced_words: After that , . just gets it and maudlin stupid

original: Big Fat Liar is just futile silliness looking to tap into the kiddie sensibilities .
swapped words: [['.', 'to'], ['just', 'the']]
replaced_words: Big Fat Liar is the futile silliness looking . tap into just kiddie sensibilities to

original: In all the annals of the movies , few films have been this odd , inexplicable and unpleasant .
swapped words: [['In', 'have'], ['the', 'movies']]
replaced_words: have all the annals of movies the , few films In been this odd , inexplicable and unpleasant .

original: A thriller without a lot of thrills .
swapped words: [['without', '.'], ['lot', 'thriller']]
replaced_words: A lot . a thriller of thrills without
```
- Augmentation is applied to randomly selected subset of data
```
1.0    1296
0.0     553
2.0     522
Name: labels, dtype: int64
```

## Final data
- We then merge all the above data points with the original data
```
1.0    16035
0.0     7149
2.0     7063
Name: labels, dtype: int64
```

# Training
- We divide our dataset into training and validation with ratio 85:15
- We define the model with LSTM cell
```
classifier(
  (embedding): Embedding(22699, 300)
  (encoder): LSTM(300, 100, num_layers=2, batch_first=True, dropout=0.2)
  (fc): Linear(in_features=100, out_features=3, bias=True)
)
The model has 7,051,603 trainable parameters
```
- We train the model for 10 epochs
```
	Train Loss: 1.024 | Train Acc: 52.17%
	 Val. Loss: 1.026 |  Val. Acc: 51.97% 

	Train Loss: 1.009 | Train Acc: 53.30%
	 Val. Loss: 1.015 |  Val. Acc: 52.04% 

	Train Loss: 0.975 | Train Acc: 55.87%
	 Val. Loss: 0.995 |  Val. Acc: 53.73% 

	Train Loss: 0.915 | Train Acc: 63.19%
	 Val. Loss: 0.969 |  Val. Acc: 56.82% 

	Train Loss: 0.854 | Train Acc: 70.68%
	 Val. Loss: 0.946 |  Val. Acc: 59.57% 

	Train Loss: 0.804 | Train Acc: 75.80%
	 Val. Loss: 0.929 |  Val. Acc: 61.46% 

	Train Loss: 0.763 | Train Acc: 80.16%
	 Val. Loss: 0.909 |  Val. Acc: 63.42% 

	Train Loss: 0.730 | Train Acc: 83.36%
	 Val. Loss: 0.896 |  Val. Acc: 65.09% 

	Train Loss: 0.706 | Train Acc: 85.70%
	 Val. Loss: 0.891 |  Val. Acc: 65.29% 

	Train Loss: 0.689 | Train Acc: 87.29%
	 Val. Loss: 0.887 |  Val. Acc: 65.53% 
```
- We are able to achieve 65% validation accuracy

# Inference
## Correct classification
```
['Negative', "A well paced and satisfying little drama that deserved better than a ` direct - to - video ' release ."]
['Positive', 'This surreal Gilliam - esqe film is also a troubling interpretations of Ecclesiastes _ # : # .']
['Negative', 'Just a Kiss wants desperately to come off as a fanciful film about the typical problems of average people .']
['Neutral', 'lively and engaging examination of how can family .']
['Neutral', 'and stylish , also somewhat hermetic']
['Positive', "-LRB- Tries -RRB- to parody a genre that 's already a joke in the United States ."]
['Negative', 'We do get the distinct impression that this franchise is drawing to a close .']
['Negative', 'A that the less charitable might as a castrated cross Highlander and Lolita .']
['Positive', 'Intriguing and .']
['Negative', 'Or Tom Green as Han Solo ?']
['Negative', 'dramatization of sexual a bit too time on its fairly ludicrous plot .']
['Positive', "captures that perverse element of the Kafkaesque where identity , overnight , is robbed and replaced with a persecuted ` ` other . ''"]
['Negative', 'The most consistently funny of the Austin Powers films .']
['Positive', 'An unencouraging threefold expansion on the former MTV series , accompanying the stunt - hungry dimwits in a random series of collected gags , pranks , pratfalls , dares , injuries , etc . .']
['Negative', 'Thanks to confident filmmaking and a pair of fascinating performances , the way to that destination is a really special walk in the woods .']
['Positive', 'hilarious . Often']
['Neutral', 'The movie quickly drag on becoming boring and predicable .']
['Positive', 'To Blandly Go Where We Went Movies ... Ago 8']
['Negative', "Farrell ... thankfully manages to outshine the role and successfully plays the foil to Willis 's world - weary colonel ."]
['Neutral', 'Only two words will tell you what you know when deciding to see it : Anthony .']
['Neutral', 'that Craven endorses They because movie makes his own look much by comparison .']
['Negative', 'A sly dissection of the inanities of the contemporary music business and a rather sad story of the difficulties of artistic collaboration .']
['Neutral', 'By the time the surprise ending is revealed , interest can not be revived .']
['Positive', 'Suffers from over - familiarity since British filmmakers have strip - mined formula since 1997 .']
['Negative', "You do n't know whether to admire the film 's stately nature and call it classicism or be exasperated by a noticeable lack of pace ."]
```
## Wrong classification
```
['Negative', 'Neutral', 'significantly less charming than listening to a four - year - old with a taste for recount his Halloween trip to the House .']
['Positive', 'Negative', "'s an visit , this ."]
['Neutral', 'Negative', "The movie has the preternatural ability to Right itself exactly when you think it 's in danger of going wrong ."]
['Positive', 'Neutral', "It 's loathsome , really is and it makes no sense ."]
['Neutral', 'Negative', "A mixed bag of a comedy that ca n't really be described as out of this world ."]
['Neutral', 'Negative', 'combination of standard , stiff TV - style animation and digital effects that do little disguise the fact the characters barely move']
['Neutral', 'Negative', "If have n't the lately be at the variety of tones in 's work ."]
['Negative', 'Neutral', 'an overwrought , the film as well it does the performances']
['Positive', 'Negative', 'The performances are so overstated , the effect off .']
['Positive', 'Negative', "You can sip your and your Ivory I 'll settle for nice cool glass of tea and a Jerry any of week ."]
['Neutral', 'Negative', 'Plodding , peevish and gimmicky .']
['Positive', 'Negative', "... ambition is in short supply in the cinema , and Egoyan tackles his themes and explores his characters ' crises with seriousness and compassion ."]
['Neutral', 'Positive', 'under - rehearsed and lifeless']
['Negative', 'Neutral', 'An unpredictability blend of gal - pal smart talk , romance comedy and dark tragedies that bites off considerably more than writer\\/director John McKay can swallow .']
['Neutral', 'Negative', "Do n't even bother to rent this on video ."]
['Neutral', 'Negative', 'The beautifully choreographed kitchen ballet is simple but absorbing .']
['Positive', 'Negative', 'This action - thriller\\/dark comedy is one of the most repellent pop a year already littered celluloid garbage']
['Neutral', 'Negative', 'Cho continues her of outer limits raunch considerable brio .']
['Neutral', 'Negative', 'Affable if not timeless , Like Mike raises some worthwhile themes while delivering a wholesome fantasy for kids .']
['Positive', 'Negative', "Maneuvers skillfully through the plot 's hot brine -- until it 's undone by the sogginess of its contemporary characters , and actors ."]
['Neutral', 'Negative', "It 's 13 months and 295 preview since I walked out , but really earned my indignant preemptive"]
['Neutral', 'Negative', 'Credibility levels are low and character development a non - starter .']
['Negative', 'Neutral', 'From beginning to end , this overheated melodrama plays like a student film .']
['Positive', 'Negative', 'to']
['Negative', 'Neutral', 'Super Troopers is an odd amalgam of comedy genres , existing somewhere between the often literal riffs of early Zucker Brothers\\/Abrahams films , and the decidedly foul stylings of their post - modern contemporaries , The Farrelly Brothers .']
```
