#  mid-range back-end developer hiring process ( with notes for when the Lead Developer is not here):

This process starts when we get a CV from one of our approved technical recruiters (we're not allowed to use other recruiters.)  Both the department head and the Lead Developer will be sent the same CVs at the same time.


## Step 1:  Initial review of the CV
[If the Lead Dev is not available this role may be fulfilled by a Senior Developer]
The lead developer should review the supplied CV.  They should look for a least a year's worth of solid experience (as in, not being a part of a team that did this but doing this themselves, and doing it as a primary part of their job, not somethign they touched on for a week on one projct) in the following skills:

  * Ruby & Rails (Look for versions if possible, Rails 4 and greater is preferrable.)
  * Test-Driven development
  * Git and github-style Pull Request-driven code review and approval.
    Be careful about people describing 'full stack' experience, look to make sure there's sufficent time spent coding back-end ruby code and not just frontend and javascript.

If there is not sufficient experience in any of the above three skills, reply to the recruiter and let them know we will not move to an intial phone interview.

If there is a good match for the skills, notify the recruiter that we'd like to have an intial 1-hour phone interview with the candidate.  You will need to communicate with the recruiter to find a time for this interview.  The Department Head is also usually present for this phone interview.  You'll need to co-ordinate eveyrone's schedles to find a time to speak with them.  These often have to happen outside or regular working hours or during lunch (sorry.)

## Step 2:  Initial Phone Interview
[If the Lead Dev is not available this role may be fulfilled by a Senior Developer]

The intial phone interview should be done by the Lead Dev and Department Head.  This is a chance to introduce ourselves to the candidate and to field any questions.  If we're not sure about the developer's level of experience with the three areas listed in Step 1, we should ask questions to clairify their experience.

### Phone interview structure:
  * Introductions and opening
  * Department head introduces the company
  * Lead Dev introduces themselves
  * Ask if the interviewee has any questions so far, answer them if there are
  * Ask about the following things, using the CV as a kicking off point.  Again, we're looking for solid personal experience with:
    * Coding Ruby on Rails (which versions)
    * Test-driven development (which frameworks have they used)
    * Using git and Pull Request-driven code review processes
    * Agile methodologies used as part of an agile team
    * How have they documented their code in a way to let other devs easily work on it should they need to?
    * What build and depolyment systems have they used?
    * What remote working experience they have, how has it worked for them?
  * Any follow-up questions that arose from the interview so far
  * Ask for further questions from either side
  * Explain next steps, tell them that if we proceed with their interview they will be given a technical test and our expectations about that (let them know we'll need a github ID to give them permission to access the test)
  * End interview

If either the Lead Dev or Department Head have doubts about the interviewee we can e-mail the recuiter clairifying questions but as a rule of thumb we don't proceed past this point if we don't feel like the candidate is strong.

If both the Lead Dev and the department Head feel that Step 2 went well we should inform the recruiter that we're reading to pass along a technical exam, if we don't already have one we'll need to ask the recruiter for the interviewee's github ID.


## Step 3: Technical Test
[If the Lead Dev is not available this role may be fulfilled by a Senior Developer]

* Prepare the technial test:
  * Clone this repo to your local machine: https://github.com/moneyadviceservice/MAS-Technical-Test-Ruby
  * Rename the directory to be XX-Technical-Test (where XX is the initals of the applicant)
  * In the newly renamed directory remove the hidden .git directory  
        ( rm -rf .git )
  * Initalise the repo, This needs to be done to remove the previous git checkin history of this repo
        ( git init )  
  * On the MAS github page ( https://github.com/moneyadviceservice ) create a new repo with the same name as the directory above ( XX-Technical-Test )
  * Following the directions on the newly created repo on github, link the gihub repo to your new local repository and push it up to github
  * Add the candidate to the repo as a collaborator and give them permission to read and write to the repo
    * Make a private channel in slack with a link to the github repo in the channel topic.  Invite the back-end developers the Lead Dev and the Department Head to the channel.
    * Once the candidate has finished with the test (we should get an email from the recruiter when they're done) ask in the slack channel for people to review the exam
    * If all of the back-end developers agree that the candidate's test is adequately completed move to step 4, if not notify the recruiter we're not proceeeding with this specific candidate

## Step 4: Second interview, preferrably in person, with the development team.
* Communicate with the recruiter, and senior developers (at least one, preferrably both) to find a time for the candidate to come in and speak with us. [It is alright for the same senior developer to be doign this part of the interview process as the others if they were doing it in the absense of the lead developer]
* The led dev or department head will greet the candidate at the interview, and bring them in for the final face to face with the developers.  
* The developers should go over the exam with the candidate, speaking about any issues that might have been brought up with the candidate's technical exam.  This is a less formal session and also a chance for the developers to get a feel for the candidate and to think about if they would work on the team.
* After about 40-45 mins with the developers the dev lead / dept head should come back in the room for some last discussion and questions from the candidate.  
* The candidate should be thanked for the time at the end of the interview for their time and let out of the building.
* The developers and lead/dept head should then discuss if they feel an offer should be made.  Again, preference should be given to no votes.  We want the decision to be unanimous in the positive for an offer to be made.