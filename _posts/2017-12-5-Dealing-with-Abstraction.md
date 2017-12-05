---
layout: post
title: Dealing with Abstraction (in React)
---

My team's unofficial team motto is "let reuse drive" - meaning let the need for abstraction come as needed. This motto isn't a law, especially when architecting a whole system, but a principle when developing business logic and front end components. Below is an example of an abstracted component.


```
import DebounceInput from 'a-helper-for-debounced-input';

const ControlSearch = ({ fetchData }) => (
  <div className="search-box search-filters">
    <div className="input-group">
      <span className="input-group-addon"><i className="fa fa-search" /></span>
      <DebounceInput
        className="form-control search-box-input"
        minLength={2}
        debounceTimeout={500}
        placeholder="Search events by name"
        onChange={e => fetchData({ search: e.target.value })}
      />
    </div>
  </div>
);

export default ControlSearch;

------

import ControlSearch from './ControlSearch';

export default () => (
  <ControlSearch fetchData={APILOOKUP} />
);
```

It's a debounced search box. It has pretty clear parameters and a single prop in order to work. This file is copy-pasted across my team's features because this is our ideal level of abstraction. Utilizing another package - `<DebounceInput />` - wraps all our necessary abstraction away. Below is an example of building this feature with full extensibility by allowing this component to be fully "abstracted". 

```
import DebounceInput from 'a-helper-for-debounced-input';

const ControlSearch = ({ 
  debounceTimeout = 500,
  iconClass = 'fa-search',
  fetchData,
  placeholder = 'Search events by name',
  queryParam = 'search',
  wrapperClass = '',
}) => (
  <div className={`search-box search-filters ${wrapperClass}`}>
    <div className="input-group">
      <span className="input-group-addon"><i className={`fa ${iconClass}`} /></span>
      <DebounceInput
        className="form-control search-box-input"
        minLength={2}
        debounceTimeout={debounceTimeout}
        placeholder={placeholder}
        onChange={e => fetchData({ search: e.target.value })}
      />
    </div>
  </div>
);

export default ControlSearch;

------

import ControlSearch from './ControlSearch';

export default () => (
  <ControlSearch fetchData={APILOOKUP} />
);
```

By building the component this way, we'd have to pass down a placeholder and the fetchData prop. That feels good, but what happens once we need to adjust the timeout? Now we have an optional prop that has a default written in this component. That's not terrible. But what happens when we need to populate the search icon differently? Now we need an additional prop that helps us define this. If this was split out into an abstracted component we'd slowly be building up the lines of code to create a simple wrapper around our `<DebouncedInput />`. 

It's a slippery slope for reuse and I think the abstraction that is most helpful is already there with the first example. It's an obvious pattern  to propagate into new files and it's honestly not too much additional HTML/JS (~25 lines). That feels like a tight component that serves it's feature set really well. A common component (like in our 2nd example) would only reduce 12 lines of HTML (if we abstract it out) and introduce who knows how many more lines of props beyond what I've shown.

It's not just about tweaking the good patterns in what you have, but it's about the eventual tweaks you'll need to make for any new functionality. Once you've reached a very good abstraction with the current implementation sometimes abstracting it further is only going to complicate future iterations.
