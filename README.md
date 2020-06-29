# ClockifyAPI
This code should be a starting point for taking data from Clockify's API and putting it into an excel sheet to hold yourself accountable. Can also be scaled up for companies that use Clockify to manage multiple employees. In the sample code, there's an added functionality where the report will get sent to a discord server using Discord's Webhook system. If you are on windows, you can automate the system entirely by going to your system's Task Scheduler, and then scheduling this script to run every time at the end of your work day.

Motivation: I realized there's a lot I could do with all the free time I had during quarantine; however, I felt that without a system to hold myself accountable, I would not follow my daily plans and instead deviate into the wrong direction. Hence, I made this python script which connects to Clockify's API (what I'm using to keep track of the daily tasks I'm doing) and then sends an accountability report to me via Discord. 

What's in it:
- Connecting to Clockify's API
- Converting the time and using Clockify's documentation to set specific time parameters
- Calculating the duration of each project for each day
- Writing the data into an excel sheet
- A linear algorithm for calculating a score for the day, based on the expected and actual time spent on a task, as well as its importance
- A simple grade checking system that is based off US Public Schools' grading scale
- Writing the saved data (score, grade, date today) into an excel sheet that updates automatically
- Implementing a Discord Webhook using the given library, which gives an accountability report every day

What's not in it:
- The code is designed to be for individuals who want to hold themselves accountable. Code is largely extendable for managing multiple employees; however, the API requests have to be changed
- The ability to add projects to both the code and the excel sheet at the same time. It assumes that you will have pretty set tasks every day and so it's largely static in that sense
- Sending professional reports/reports in a more professional way. Discord is not very professional, but it was the easiest way to send a report to a server (aside from using Gmail)



