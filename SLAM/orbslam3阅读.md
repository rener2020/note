---
title: orbslam3阅读
category: 2022-07-21
renderNumberedHeading: true
grammar_cjkRuby: true
---


光束法平差
- Short-term data association
   matching map elementsobtained during the last few seconds. This is the onlydata association type used by most VO systems, thatforget environment elements once they get out of view,resulting in continuous estimation drift even when thesystem moves in the same area
- Mid-term data association
   matching map elements thatare close to the camera whose accumulated drift is stillsmall. These can be matched and used in BA in the sameway than short-term observations and allow to reach zerodrift when the systems moves in mapped areas. They arethe key of the better accuracy obtained by our systemcompared against VO systems with loop detection.
- Long-term data association