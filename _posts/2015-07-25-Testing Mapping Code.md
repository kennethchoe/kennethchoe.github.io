---
layout: post
title: "Testing Mapping Code"
published: true
---

How do you test your mapping code? You want to have your test code to raise an error when,

1. You added new property on domain model and view model, but spelled them or typed them differently so auto-mapper did not recognize them to be mapped.
2. You added new property on domain model and repository's Get() function, but did not change Update() function.

I like my tests to remind me of these mistakes. Today I wrapped set of classes I used for this purpose and published a nuget package: [ObjectTestHelpers](https://www.nuget.org/packages/ObjectTestHelpers/).

Test strategy for bi-directional mapping is very different from the one for one-way mapping. Bi-directional mapping can be verified by getting one object round-trip and see if it is the same. One-way mapping takes more thought. For more details about ObjectTestHelpers and the usage, see [README.md](https://github.com/kennethchoe/ObjectTestHelpers/blob/master/README.md)
