| Address | Bedroom # | Square Footage | Predicted Price | Actual Price |
| --------------- | --------------- | --------------- |--------------- |--------------- |
| 228 Church St | 4 | 3680 | 354k | 399k |
| 160 Holly Point Road | 3 | 1238 | 153k | 97k |
| 760 New Point Comfort Highway | 5 | 3051 | 322k | 347.5k |
| 6138 East River Rd | 4 | 3524 | 342k | 289k |
| Moon | 2 | 1479 | 165k | 250k |
| 4403 New Point Comfort Highway | 3 | 2840 | 280k | 229k |

### Based on the code we wrote in class on Friday, Feb 5, I decided to update my model to include two values. Instead of running separate models for bedroom size and square footage, I stacked these values using np.stack and inputted both into my neural net. The table pictured above lists each house's bedroom # and square footage as well as the predicted price and actual price. Houses with a predicted price that is greater than its actual price are good deals according to this model. The houses that would be considered good deals are the houses on Holly Point Road, East River Road, and 4403 New Point Comfort Highway. Houses with an actual price that is greater than predicted price are bad deals according to this model. The houses that would be considered bad deals are the houses on Church Street, 760 New Point Comfort Highway, and Moon. 
