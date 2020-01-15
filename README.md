### Problem description.
Game Set is a board game designed by Marsha Falco in 1974. The deck consists of 81 unique cards that vary in four features across three possibilities for each kind of feature: number of shapes (one, two, or three), shape (diamond, squiggle, oval), shading (solid, striped, or open), and color (red, green, or purple). Each possible combination of features (e.g. a card with [three] [striped] [green] [diamonds]) appears as a card precisely once in the deck.
In the game, certain combinations of three cards are said to make up a set. For each one of the four categories of features — color, number, shape, and shading — the three cards must display that feature as a) either all the same or b) all different. Put another way: For each feature, the three cards must avoid having two cards showing one version of the feature and the remaining card showing a different version.
For example, 3 solid red diamonds, 2 solid green squiggles, and 1 solid purple oval form a set, because the shadings of the three cards are all the same, while the numbers, the colors, and the shapes among the three cards are all different.
We developed the solution to find set on the deck quickly and help the player to win the game. 

### Data investigation and preprocessing.
As an input, our algorithm takes the picture of the deck with cards. Then it crops every card to get a photo of each card for further processing. Then we extract color, number of elements, shape, and fill of the figure.


### Solution.
For the detection of the color, we first tried to classify the colors on the image by using K-means to find the most dominant colors in RGB format on it. The logic behind using this approach was the following if we have only white color and purple/green/red, then we can define if the green color is the most represented at the photo, so from RGB color green will have the highest digit. But this approach worked only for pictures that were made in the place with good lighting. When we have images with shades on it or images that were made in the place with bad light, this approach stops working. Also, we tried to raise the brightness of the photo and somehow reduce the impact of the shades, but still, this approach didn't work appropriately.

The next approach we’ve used was to change the image to HSV colorspace. We applied masks to separate red, green and blue colors and looked at those ones that returned result different from 0 (not black frame). After that, we’ve calculated the top color on this picture, which was basically the one that got masked, so the color of the figure on the image. 



For detecting the number of elements on the cards, first we tried to thresholded the image with these parameters (cv2.threshold(blur, 120, 255, cv2.THRESH_BINARY_INV)[1] ) and it worked pretty good for pictures that were taken in a place with good lighting. But when it came to the bad pictures with shades and noise, this approach didn't work either. Also, we tried to use a Gaussian blur to reduce image noise, but even this filter didn't help the algorithm to work better.

After some thinking we’ve decided to combine shape and number of shapes on the image and calculate both of those by using the area of the contour. We defined a threshold for different shaped based on their area value and then calculated the number of shapes on the image by counting how many times the object with this area (or area that is very close to this one) appears on the image. 

Finally, we calculated the filling on the figure on the card. Our first ideas were to simply compare the number of the contours, as we assumed it would be equal to the number of the figure when the figure is solid, twice as big for empty and big for lines, but after a closer look we realized that that idea was wrong. So we decided to calculate this in a different way. In the image that was returned when calculating color (that is a black background, colored shape) calculate the number of colored pixels.  If the rate (comparison of colored and total ) is big enough, it means that the filling is "filled". Otherwise, we detect canny edges and calculate its sum. If it greater than threshold, return "lines" or "empty" otherwise. 

There is no efficient algorithm for Detecting SET on the desk, that doesn't use if statements. So firstly, we decided to create all possible combinations from the cards that we have on the desk and then check if it is SET. The algorithm detects if the cards have all three different colors/the same color if the conditions of the SET game are met, and these cards are SET, it checks the number of figures and performs a validation of rules and then do the same with other characteristics of cards.

###Results and evaluation.
We have tested it on several pictures having both sets and no sets, with different lighting and different picture quality and it works in most of the cases. The only problem we get is when the light is too smooth, so it is hard to distinguish between red and blue. Also when it is super bright, empty and filled with lines are being confused sometimes, but this algorithm is better than the one we’ve started with. So those are two things that need to be improved and we would work on if we had more time. 
