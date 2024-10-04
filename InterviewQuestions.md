### [React + Node + JavaScript] Mixed Technical Questions

1. What is ***reconciliation***?

    Answer: Reconciliation is the process by which React updates the DOM. It is the process of comparing the previous copy of virtual DOM with current copy of virtual DOM using diffing algorithm and find the difference or the nodes that needs to be update into the actual DOM and then updating the actual DOM accordingly.

    React uses a technique called ***two phase commit*** to update the DOM. In the first phase, React updates the virtual DOM. In the second phase, React updates the actual DOM.


2. Explain ***spread*** and ***rest*** operator.

    Answer: Spread operator is used to expand an array or an object into individual elements.
    ```
    // Array example
    const array1 = [1, 2, 3];
    const array2 = [4, 5, 6];

    // Using spread to combine arrays
    const combinedArray = [...array1, ...array2];
    console.log(combinedArray); // Output: [1, 2, 3, 4, 5, 6]
    ```

    ```
    // Object example
    const obj1 = { a: 1, b: 2 };
    const obj2 = { c: 3 };

    // Using spread to combine objects
    const combinedObject = { ...obj1, ...obj2 };
    console.log(combinedObject); // Output: { a: 1, b: 2, c: 3 }
    ```
    
    Rest operator is used to merge multiple elements into a single element.
    example:
    ```
    // Array example
    const [a,b,...rest] = [0, 1, 2, 3, 4, 5, 6];
    console.log(rest); // [2, 3, 4, 5, 6]
    ```

    ```
    // Object example
    const {a,b,...rest} = {a: 1, b: 2, c: 3, d: 4};
    console.log(rest); // {c: 3, d: 4}
    ```
3. Why we use ***.bind*** in class component?

    Answer: .bind is used to bind the function to the class component. It is used to bind the function to the class component so that the function can be used as a method of the class component.




### Scenario based questions

1. If our application into public domain and a hacker gets access to accesstoken and api and he hits our api numerous times, then how we can secure our api and resources for load or *DDOS*

   Answer: To protect your API and resources from potential DDoS attacks and unauthorized access, you can implement several security measures:

   1. Rate Limiting:
      Implement rate limiting to restrict the number of requests a client can make within a specific time frame.

      Example using Express.js and the `express-rate-limit` middleware:

      ```javascript
      const rateLimit = require("express-rate-limit");

      const apiLimiter = rateLimit({
        windowMs: 15 * 60 * 1000, // 15 minutes
        max: 100 // Limit each IP to 100 requests per windowMs
      });

      app.use("/api/", apiLimiter);
      ```

   2. API Key Rotation:
      Regularly rotate API keys and invalidate old ones.

      ```javascript
      function rotateApiKey(userId) {
        const newApiKey = generateNewApiKey();
        const expirationDate = new Date(Date.now() + 30 * 24 * 60 * 60 * 1000); // 30 days
        storeApiKey(userId, newApiKey, expirationDate);
        invalidateOldApiKey(userId);
        return newApiKey;
      }

      scheduleJob('0 0 1 * *', () => { // Run monthly
        getAllUsers().forEach(user => rotateApiKey(user.id));
      });
      ```

   3. IP Whitelisting/Blacklisting:
      Maintain lists of trusted or blocked IP addresses.

      ```javascript
      const ipFilter = require('express-ipfilter').IpFilter;

      const whitelist = ['127.0.0.1', '::1'];
      app.use(ipFilter(whitelist, { mode: 'allow' }));

      // Or for blacklisting:
      const blacklist = ['123.456.78.9'];
      app.use(ipFilter(blacklist, { mode: 'deny' }));
      ```

   4. Web Application Firewall (WAF):
      Use a WAF to filter and monitor HTTP traffic. This often requires a separate service or cloud provider configuration.

   5. CAPTCHA or Challenge-Response:
      Implement CAPTCHA for suspicious requests.

      ```javascript
      const recaptcha = new Recaptcha('SITE_KEY', 'SECRET_KEY');

      app.post('/api/endpoint', (req, res) => {
        recaptcha.verify(req, (error, data) => {
          if (!error) {
            // CAPTCHA verified, process the request
          } else {
            res.status(400).send('CAPTCHA verification failed');
          }
        });
      });
      ```

   6. OAuth 2.0 with short-lived tokens:
      Use OAuth 2.0 for authentication and authorization.

      ```javascript
      const OAuth2Server = require('oauth2-server');
      const oauth = new OAuth2Server({
        model: require('./auth-model.js'),
        accessTokenLifetime: 3600, // 1 hour
        refreshTokenLifetime: 1209600, // 14 days
      });

      app.post('/oauth/token', (req, res) => {
        const request = new OAuth2Server.Request(req);
        const response = new OAuth2Server.Response(res);
        oauth.token(request, response)
          .then((token) => res.json(token))
          .catch((err) => res.status(err.code || 500).json(err));
      });
      ```

   7. Request Throttling:
      Implement progressive delays for repeated requests.

      ```javascript
      const requestCounts = new Map();

      function throttleRequests(req, res, next) {
        const clientIP = req.ip;
        const currentCount = requestCounts.get(clientIP) || 0;
        
        if (currentCount > 10) {
          const delay = Math.pow(2, currentCount - 10) * 1000; // Exponential backoff
          setTimeout(() => {
            requestCounts.set(clientIP, currentCount + 1);
            next();
          }, delay);
        } else {
          requestCounts.set(clientIP, currentCount + 1);
          next();
        }
      }

      app.use(throttleRequests);
      ```

   8. Monitoring and Alerting:
      Set up real-time monitoring and alerting systems.

      ```javascript
      const expressStatusMonitor = require('express-status-monitor');
      app.use(expressStatusMonitor());

      app.use((req, res, next) => {
        const requestsPerMinute = getRequestsPerMinute();
        if (requestsPerMinute > 1000) {
          sendAlert('High traffic detected: ' + requestsPerMinute + ' requests per minute');
        }
        next();
      });
      ```

   9. Content Delivery Network (CDN):
      Use a CDN to distribute content and absorb traffic. This typically involves configuring a service like Cloudflare, Amazon CloudFront, or Akamai.

   10. API Gateway:
       Implement an API gateway to centralize request handling and security policies. Services like Amazon API Gateway or tools like Kong can be used for this purpose.

   11. Input Validation:
       Thoroughly validate and sanitize all input.

       ```javascript
       const { body, validationResult } = require('express-validator');

       app.post('/api/user', [
         body('username').isLength({ min: 5 }),
         body('email').isEmail(),
         body('password').isLength({ min: 8 })
       ], (req, res) => {
         const errors = validationResult(req);
         if (!errors.isEmpty()) {
           return res.status(400).json({ errors: errors.array() });
         }
         // Process the request
       });
       ```

   12. HTTPS:
       Ensure all API communications are encrypted using HTTPS.

       ```javascript
       const https = require('https');
       const fs = require('fs');

       const options = {
         key: fs.readFileSync('path/to/key.pem'),
         cert: fs.readFileSync('path/to/cert.pem')
       };

       https.createServer(options, app).listen(443);
       ```

   13. Logging and Auditing:
       Implement comprehensive logging and auditing.

       ```javascript
       const winston = require('winston');

       const logger = winston.createLogger({
         level: 'info',
         format: winston.format.json(),
         defaultMeta: { service: 'user-service' },
         transports: [
           new winston.transports.File({ filename: 'error.log', level: 'error' }),
           new winston.transports.File({ filename: 'combined.log' })
         ]
       });

       app.use((req, res, next) => {
         logger.info(`${req.method} ${req.url}`, { 
           ip: req.ip, 
           user: req.user ? req.user.id : 'anonymous' 
         });
         next();
       });
       ```

   By combining these measures, you can significantly enhance the security of your API and protect your resources from unauthorized access and DDoS attacks. Regular security audits and staying updated on the latest security best practices are also crucial for maintaining a robust defense against evolving threats.