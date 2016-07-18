# RTView-Cookbook

Welcome to the RTView Cookbook.

This cookbook contains a number of "recipes" (referred to herein as "Patterns") that show solutions to the most commonly required tasks using RTView.

The intention of the cookbook is to provide a reference to these tasks while clearly showing how they can be accomplished by an end user of any experience level. Completed demos are provided for the cookbook, but we suggest that the recipes are followed as they contain a great deal of "best practice" information and clarify steps that are sometimes subtle enough to be overlooked that are important to a properly running visualization.

# System Requirements
Java 1.5+
RTView Core 6.x

# Downloads
## RTView Core: http://sl.com/evaluation-request/
## Java: http://www.oracle.com/technetwork/java/javase/downloads/index.html

# Using The Cookbook

Please make certain that the environment variable RTV_USERPATH inlcudes the simulated data used by the cookbook, mysimdata.jar.

This is done by adding "mysimdata.jar" to RTV_USERPATH as a global environment variable, or by manually setting RTV_USERPATH from the command line each time the cookbook is used. Mysimdata.jar has been included in each pattern, but if you wish to add it to the lib path then it sohuld be added as a relative path explicitly (e.g. c:\Program Files\RTV_xxx\lib\mysimdata.jar").

# Patterns

##Pattern 1 - Create and Display a Current Value Table
##Pattern 2 - Show a Subset of Incoming Data in a Table
##Pattern 3 - Trend Current asnd Historical Data
##Pattern 4 - Show Multiple Trends of Synchronous Data
##Pattern 5 - Show Multiple Trends of Asynchronous Data
