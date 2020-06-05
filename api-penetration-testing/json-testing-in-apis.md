# JSON testing in APIs

**Types of JSON data and how to fuzz them**

* strings : whatever u want 191919
* number : 123
* object : { }
* array : \[ \]
* boolean : true/false
* null : null

try mixing data formats as welll as trying different values while fuzzing

for exaxmple, auth token should be string, therefore try…

* {"authtoken": true, ...}
* {"authtoken": \[\], ...}
* {"authtoken": {}, ...}
* {"authtoken": 0, ...}
* {"authtoken": \[true, "your-secret"\], ...}

for **DDoS** or creating digital server retardation, this is the biggest number you can put in \(128 or 256 bits depending on parser\),  input it in number parameters \(i.e. limit, per\_page, etc..\)

{..., limit: 10e307}

