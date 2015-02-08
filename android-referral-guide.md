Android Referral Program Guide
=========================
This quick start guide shows you how to leverage Branch links to build a powerful referral system for your app.

# Table of Contents

1. [What Branch can do](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/android-referral-guide.md#what-branch-does)
1. [Referral system design considerations](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/android-referral-guide.md#referral-system-design-considerations)
1. [Basic Account Registration](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/android-referral-guide.md#basic-account-registration)
1. [SDK Initialization](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/android-referral-guide.md#sdk-initialization)
1. [Event Tracking and Reward Rules](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/android-referral-guide.md#event-tracking-and-reward-rules)
1. [Link Creation for User Attribution](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/android-referral-guide.md#link-creation-for-user-attribution)
1. [Identity Management for Influence Tracking](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/android-referral-guide.md#identity-management-for-influence-tracking)
1. [Testing Considerations](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/android-referral-guide.md#testing-considerations)
1. [Advanced: Coupon/Referral Codes](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/android-referral-guide.md#advanced-couponreferral-codes)
1. [Advanced: Extending Referrals to the Web](https://github.com/BranchMetrics/Branch-Integration-Guides/blob/master/android-referral-guide.md#advanced-extending-referrals-to-the-web)


# What Branch Does

For a referral system, Branch provides:
_- User attribution through a tracking, download URL_
In short, we tell you when an existing user who you empower with a Branch link, drives a new install.

_- (optional) Custom rewarding rules tied to events (install, signup, purchase, etc) in app_
We allow you to tie reward events to special 

_- (optional) User reward tracking/storage (integer balance)_
We leave the actual user facing rewarding to you, but we store how many credits have been earned through our reward rules. This makes it easy so that you can just check the balance of credits in the app from us, give the user some reward, then clear the credit balance on our server.

_- (optional) Credit transaction history_
At any time, via API or SDK, you can retrieve the full credit history of the user

_- (optional) Coupon codes for each user_
You can use our system to generate referral codes and redeem them through our system. We track which user owns the code and gets rewarded when a new user redeems it.

## High Level Examples

You can use just the URLs to track who was the referrer or you can use the entire credit storage and referral code solution as well.

**Example _just-the-links_ implementation:**

- Create Branch links for each user in the app
	- bundle in user ID into the dictionary
- Any user who installs from this link can access the original, inviting user ID
- You can handle rewarding.

**Example _full-credit-storage_ implementation:**

- Create Branch links for each user in the app
- Setup a reward rule to give 2000 credits to any referred user who installs from a Branch link
- Setup a reward rule to give 4000 credits to the referring user
- Retrieve credit balance from Branch server
- Apply credits to user’s order
- Redeem the credits from the Branch server

# Referral System Design Considerations

Here are a couple questions to ask yourself when you are starting to build a referral system:

_Do I want Branch to track credit balances so I don't have to?_
It's possible to just use us for the links and user attribution. If you're just planning to use the links, you only need section 4 and 6

_1. Do I want to reward both users, the user who drove the new install or just the new user, and what will the reward be?_


_1. Do I want to reward only fresh installs or existing users as well_
If you want to reward only fresh installs, you'll want to tie our rewards to the 'install' action. If you want to reward both users, you'll want to tie the reward rule to the 'referred session' action.

# Basic Account Registration

# SDK Initialization

# Event Tracking and Reward Rules

# Link Creation for User Attribution

# Identity Management for Influence Tracking

# Testing Considerations

# Advanced: Coupon/Referral Codes

# Advanced: Extending Referrals to the Web
