# Beddit web API documentation (Beta)

[Beddit](http://www.beddit.com) is a sleep tracker device and mobile
application. It has an ultra-thin film sensor that you place in your bed, under
the bed sheet. All you have to do is to sleep on it. Beddit connects wirelessly
to your smartphone for a sleep analysis. It measures sleep stages, heart rate,
breathing, snoring, and more.

When Beddit user signs in to Beddit, all sleep data is automatically synced to
Beddit servers. The web API gives developers possibility to make applications
that use Beddit sleep data.

The API is free for all during the Beta period. When data from the API,
please show the text "Sleep data provided by Beddit" alongside the data.
Beddit reserves the right to make changes to the API during the Beta period.

The documentation is divided into following chapters.

## [General](1-General.md)

Documents the general conventions used throughout the API.

## [Authentication](2-Authentication.md)

All access to user data in Beddit is authenticated with access tokens. This
chapter describes how authentication works and how access token is obtained.

## [Resources](3-Resources.md)

This chapter describes the resources for retrieving sleep data.
