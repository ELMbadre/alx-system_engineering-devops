# Postmortem

![Flogging a dead horse](post-mortem-meetings.jpeg)

## Incident Summary

Shortly after deploying a new feature on our Ruby on Rails site, we began receiving numerous complaints from users who were unable to sign in or register on the platform. Within 5 minutes of the update, around 127 users reached out, indicating a major issue. Although the feature had functioned properly during our local tests, something clearly went wrong in the live environment. We quickly realized that the site couldn’t even start up after we cloned the repository and followed the setup instructions. After a brief investigation, we identified the root cause: a failure to update the project dependencies. The site was down from 9:55 AM GMT+1 until 11:20 AM GMT+1.

## Timeline

- **05-02-2022 9:55 AM GMT+1:** The first customer reported being unable to sign in.
- **05-02-2022 10:20 AM GMT+1:** Winus, one of our backend developers, replicated the issue.
- **05-02-2022 10:35 AM GMT+1:** We began inspecting the controllers and views for potential issues.
- **05-02-2022 10:40 AM GMT+1:** We suspected that the bcrypt gem, responsible for password hashing, was the source of the problem due to an error indicating an invalid hash.
- **05-02-2022 10:42 AM GMT+1:** We checked if the form fields were correctly bound to the model fields but found no issues there.
- **05-02-2022 10:45 AM GMT+1:** We incorrectly assumed that the issue might be with the hash generation process in the controllers.
- **05-02-2022 10:50 AM GMT+1:** Winus speculated that the password hashing process might be flawed.
- **05-02-2022 11:00 AM GMT+1:** The incident was escalated to the backend team for further investigation.
- **05-02-2022 11:20 AM GMT+1:** The problem was resolved by updating the bcrypt gem to a newer version, after which the site functioned correctly.

## Root Cause and Fix

The issue was caused by using an outdated version of the bcrypt gem, which failed to process valid password hashes correctly. Winus resolved the problem by updating the Gemfile.lock with the latest version of bcrypt and reinstalling the dependencies, which restored the site’s functionality.

## Preventative Actions

- Implement a continuous integration (CI) pipeline to run builds for every pull request. This will ensure that all changes are tested before being merged into the deployment branch.
- Set up monitoring tools for the database and application servers to detect any potential issues early.
- Develop comprehensive tests for all new features. These tests should pass before any code is merged into the deployment branch.

