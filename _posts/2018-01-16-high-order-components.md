---
layout: post
title:  "Understanding higher-order components"
date:   2018-01-20 12:00:12 +0000
permalink: /coding/understanding-higher-order-components
category: coding
meta_description: >
 Understand why and how to use higher-order components with React JS 
excerpt_separator: <!--more-->
---

In this post I will discuss the why and how to use higher-order components (<abbr title="higher-order component">HOC</abbr>) with [ReactJS](https://reactjs.org/).

**Why use HOC:** Promote _reuse_ of logic across React components.

Components are the typical element for reuse in React but sometimes features don't fit into this standard. There might be similar methods used to fetch data but the display is different. An example of this is shown later.

<!--more-->

**How to use HOC:** The core structure of a HOC is a **function** that takes a **component** and returns a **new component**. HOC are _pure functions_ with no side-effect because the component passed in, is wrapped in a new component. Typically **data is injected as a prop** and additional props are appended to the component.

This is a general compositional pattern and not part of React as such.

## How do you decide?

I would recommend first building components in the normal React way. When the application is working as expected review your components to identify shared behaviours. Build the behaviour in a generic enough fashion to work for all existing components. Following this will improve your experience in identify HOCs, even before completing components but I would hold back and treat it as a refactoring step when deciding to build a new HOC.

## Fetch data higher-order components

First the working components which have similar data fetching behaviour.

```javascript
class VideoBlog extends React.Component {
  constructor() {
    super();
    this.state = {
      videoBlog: null
    };
  }

  componentDidMount() {
    fetch("http://example.com/videos/124").then(data => {
      this.setState({
        videoBlog: data.videoBlog
      });
    });
  }

  render() {
    return <video src={this.state.videoBlog.src} />;
  }
}

class RelatedVideos extends React.Component {
  constructor() {
    super();
    this.state = {
      videoList: []
    };
  }

  componentDidMount() {
    fetch("http://example.com/videos/related/" + this.props.videoId).then(
      data => {
        this.setState({
          videoList: data.videoList
        });
      }
    );
  }

  render() {
    return <List data={this.state.videoList} />;
  }
}
```

`VideoBlog` and `RelatedVideo` are not the same output but the implementation is similar. They both fetch data in `componentDidMount` life cycle.

What will the above components look like when wrapped in `withFetch` HOC.

```javascript
const VideoBlog = withFetch(VideoBlogView, "http://example.com/videos/124");
const RelatedVideo = withFetch(
  RelatedVideoView,
  "http://example.com/videos/related/"
);
```

### Breakdown of the above

**First param** is the component to be wrapped by the HOC. The old `VideoBlog` component changed from a class to a [functional component](https://reactjs.org/docs/components-and-props.html#functional-and-class-components) called `VideoBlogView` because it no longer needs to manage state. Same applies to `RelatedVideo`.

**Second param** is the fetch url to get data from.

The `withFetch` HOC looks like this:

```javascript
function withFetch(WrapComponent, request) {
  class WithFetch extends React.Component {
    constructor() {
      super();
      this.state = {
        data: null
      };
    }

    componentDidMount() {
      let url = request;
      if (this.props.videoId !== undefined) url = request + this.props.videoId;

      fetch(url).then(data => {
        this.setState({
          data
        });
      });
    }

    render() {
      return <WrapComponent response={this.state.data} {...this.props} />;
    }
  }

  WithFetch.displayName = `WithFetch(${getDisplayName(WrapComponent)})`;
  return WithFetch;
}
```

**State** now has a `data` property to inject data from the API fetch into the wrapped component via the `response` prop.

**ComponentDidMount** has a more generic `fetch` call to get data from any URL.

A good convention in HOCs is to set the `displayName` with the name of HOC function (`withFetch`) and component display name (`VideoBlog`) to help with debugging.

## Libraries using HOCs

Below are some libraries you might have used which are using HOCs.

`Relay.createContainer(component, graphqlQuery);` [RelayJS createContainer](https://facebook.github.io/relay/docs/en/classic/classic-api-reference-relay-container.html) follows a similar function signature to what is shown in the example above. First param is the component to be wrapped and second is the query.

`ReactRedux.connect(props, dispatch)(component);` [React Redux connect](https://github.com/reactjs/react-redux/blob/master/docs/api.md#connectmapstatetoprops-mapdispatchtoprops-mergeprops-options) has a different function signature of a function returning a function which accepts one argument of component to create a HOC. This is more complex however you might see more of this style because this promotes HOC composition (also called **enhancers**).

A library that promotes HOC composition is [Recompose](https://github.com/acdlite/recompose).

> A React utility belt for function components and higher-order components.

In the first part of Recompose documentation they provide a good [explanation about enhancers](https://github.com/acdlite/recompose/blob/master/docs/API.md)

Worth pointing out there is a [minor performance loss](https://github.com/acdlite/recompose/blob/master/docs/performance.md) when you start composing with many HOCs.

## Conclusion

Once you gain experience in building higher-order components you will start to notice your components become smaller and focused units of code. This will help make it easier to reason about your application logic. Reusing HOCs with composition means it will be a matter of plugging in relevant components to build complex features with easy.
