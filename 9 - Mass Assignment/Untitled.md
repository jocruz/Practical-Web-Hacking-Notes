What is Mass Assignment?

Mass Assignment occurs when applications (often frameworks) automatically bind parameters to objects.

This is a feature to make it a little easier to build objects based on incoming requests for developers.


JSON from POST request

"user": {
"firstName": "jeremy",
"middleName": "jackson",
"lastName": "jones"
}
｝

Node js using spread operator:

const user = { ...req.body }

jeremy 
jackson
jones



JSON from POST request

"user": {

"firstName" : "jessamy",

"middleName": "jameson",

"lastName": "johnson",

"admin": true,

}

Node js using spread operator

const user = {...req.body }

jessamy jameson johnson admin: true


How do we identify these parameters?

- ﻿﻿Fuzzing
- ﻿﻿Code review
- ﻿﻿JWT tokens
- ﻿﻿Leaky API endpoints
- ﻿﻿Front-end code
- ﻿﻿etc
