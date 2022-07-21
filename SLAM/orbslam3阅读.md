---
title: orbslam3阅读
category: 2022-07-21
renderNumberedHeading: true
grammar_cjkRuby: true
---



- Short-term data association
   matching map elementsobtained during the last few seconds. This is the onlydata association type used by most VO systems, thatforget environment elements once they get out of view,resulting in continuous estimation drift even when thesystem moves in the same area
- Mid-term data association
   matching map elements thatare close to the camera whose accumulated drift is stillsmall. These can be matched and used in BA in the sameway than short-term observations and allow to reach zerodrift when the systems moves in mapped areas. They arethe key of the better accuracy obtained by our systemcompared against VO systems with loop detection.
- Long-term data association
  matching observations withelements in previously visited areas using a place recog-nition technique, regardless of the accumulated drift (loopdetection), the current area being previously mappedin a disconnected map (map merging), or the trackingbeing lost (relocalization). Long-term matching allows toreset the drift and to correct the map using pose-graph(PG) optimization, or more accurately, using BA. This isthe key of SLAM accuracy in medium and large loopyenvironments.
  
  
 ## The main novelties of ORB-SLAM3 are:
 - A monocular and stereo visual-inertial SLAM system
   fully relies on Maximum-a-Posteriori (MAP) estimation
- Improved-recall place recognition.