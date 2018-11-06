# React Workshop

An introductory workshop to learn about React.

## Contents

1. [React elements](01-jsx)
2. [Functional components](02-components_and_props)
3. [Class-based components](03-classes_and_state)
4. [Forms in React](04-forms_and_lists)
5. [Stopwatch Workshop](05-exercise-stopwatch)

### Note

We'll be writing our code in a single `index.html` file for now. There are three scripts included on the page: React, ReactDOM and Babel. The first two expose `React` and `ReactDOM` as globals. The Babel script allows us to use a special `<script type="text/babel">` to have our JS 'transpiled' into standard ES5. This will let us write ES6 and other React specific syntax.

This set-up is simpler to jump into without having to learn about complex build-systems when you are really trying to learn React. It also demonstrates that React isn't something magical that requires modern JS and WebPack etc. It's just a JavaScript library that you can include in an HTML page.

This is in no way a best practice, so please don't build a real app this way :)

We'll look at Bundlers a bit later on, and will eventually modularise our code using [Parcel](http://parceljs.org/).

## Bonus Reading

If you're finished early, check out these entries in the React Docs. They give a slightly higher-level look at how you should think about building React apps:

1. [Lifting State Up](https://reactjs.org/docs/lifting-state-up.html)
2. [Thinking In React](https://reactjs.org/docs/thinking-in-react.html)

## License

Copyright © Progmasters (QTC Kft.), 2018.
All rights reserved. No part or the whole of this Teaching Material (TM) may be reproduced, copied, distributed, publicly performed, disseminated to the public, adapted or transmitted in any form or by any means, including photocopying, recording, or other electronic or mechanical methods, without the prior written permission of QTC Kft. This TM may only be used for the purposes of teaching exclusively by QTC Kft. and studying exclusively by QTC Kft.’s students and for no other purposes by any parties other than QTC Kft.
This TM shall be kept confidential and shall not be made public or made available or disclosed to any unauthorized person.
Any dispute or claim arising out of the breach of these provisions shall be governed by and construed in accordance with the laws of Hungary.
