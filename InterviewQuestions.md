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