# System document

## Purpose of this document

This document describes in general level how Port Activity Application works its functions in high level.

## System overview

Port activity application (PAA) is timestamp collecting and collaboration tool for all port actors. Application is split in logical modules: activity module and logistics module. Activity modules deals with all vessel related data.

Activity module collect timestamps related to port call from different sources and displays them in logical order to show progression of port call. Any port actor that uses application can “pin” vessels to get notification of any changes related to port calls within port calls.

Logistics module handles logistics timestamps. Logistics module handles logistics timestamp and  contains only simple tracking timestamps of trucks coming and leaving port. There is no any hard relation between data between activity and logistics module so port calls are not tied to logistics part anyway.

Notification tool that can be used for communication purposes within a port. Simple messages related to vessels or port can be shared between port actors (not vessels itself!).

Sea traffic management (STM) Vehicle information system (VIS) integration allows getting timestamps from STM  vessels with VIS capabilities via vessels voyage plans. Voyage plans are exchanged with Route plan exchange format (RTZ).

Since PAA itself is registered application in STM/VIS it can also share information to another PAA applications.

Application has versions to used with both web browsers and mobile applications (iOS and Android). Web version contains all functionality required view, manage and control integration data flows. Mobile application has restricted capabilities only to core views and functionality required by port actors. Yet mobile application provides push notifications delivered into user mobile phone even application is not active.

## Port activity application

## General principles

## Vessel timestamp

## Logistics timestamps

## STM ships integration

## Port to port communication

## Timestamps relation to port call

### Clustering problem
