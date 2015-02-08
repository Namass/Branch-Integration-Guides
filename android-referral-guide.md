Android Referral Program Guide
=========================
This quick start guide shows you how to leverage Branch links to build a powerful referral system for your app.

# Table of Contents

1. What Branch can do

1. Referral system design considerations

1. Basic Account Registration

1. SDK Initialization

1. Event Tracking and Reward Rules

1. Link Creation for User Attribution

1. Identity Management for Influence Tracking

1. Testing Considerations

1. Advanced: Coupon/Referral Codes

1. Advanced: Extending Referrals to the Web


# What Branch can do

For a referral system, Branch provides:
- User attribution through a tracking, download URL

In short, we tell you when an existing user who you empower with a Branch link, drives a new install.

- (optional) Custom rewarding rules tied to events (install, signup, purchase, etc) in app

We allow you to tie reward events to special 

- (optional) User reward tracking/storage (integer balance)

We leave the actual user facing rewarding to you, but we store how many credits have been earned through our reward rules. This makes it easy so that you can just check the balance of credits in the app from us, give the user some reward, then clear the credit balance on our server.

- (optional) Credit transaction history

At any time, via API or SDK, you can retrieve the full credit history of the user

- (optional) Coupon codes for each user

You can use our system to generate referral codes and redeem them through our system. We track which user owns the code and gets rewarded when a new user redeems it.

You can use just the URLs to track who was the referrer or you can use the entire credit storage and referral code solution as well.

Example _just-the-links_ implementation:

- Create Branch links for each user in the app
	- bundle in user ID into the dictionary
- Any user who installs from this link can access the original, inviting user ID
- You can handle rewarding.

Example _full-credit-storage_ implementation:

- Create Branch links for each user in the app
- Setup a reward rule to give 2000 credits to any referred user who installs from a Branch link
- Setup a reward rule to give 4000 credits to the referring user
- Retrieve credit balance from Branch server
- Apply credits to user’s order
- Redeem the credits from the Branch server

# Referral system design considerations

Here are a couple questions to ask yourself when you are starting to build a referral system:

1. Do I want Branch to track credit balances so I don't have to?

It's possible to just use us for the links and user attribution. If you're just planning to use the links, you only need section 4 and 6

1. Do I want to reward both users, the user who drove the new install or just the new user, and what will the reward be? 



1. Do I want to reward only fresh installs or existing users as well

If you want to reward only fresh installs, you'll want to tie our rewards to the 'install' action. If you want to reward both users, you'll want to tie the reward rule to the 'referred session' action.

# Basic Account Registration

# SDK Initialization

# Event Tracking and Reward Rules

# Link Creation for User Attribution

# Identity Management for Influence Tracking

# Testing Considerations

# Advanced: Coupon/Referral Codes

# Advanced: Extending Referrals to the Web
