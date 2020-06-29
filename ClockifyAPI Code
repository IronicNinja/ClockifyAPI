import requests
from datetime import date
import openpyxl
import schedule
import time
from discord_webhook import DiscordWebhook, DiscordEmbed

headers = {
    'content-type': 'application/json',
    'X-Api-Key': '[your API key]'
}

### format date correctly
d = date.today().strftime("20"+"%y-%m-%d")
start_time = "T10:00:00.000Z" # start time of day
end_time = "T20:00:00.000Z" # end time of day

### dict of dates
payload = {
    'start': d+start_time,
    'end': d+end_time
}

### project id to project name
project_nameids = {
    '890dfafasfd98dfasf908afd': 'Example1',
    'faf7a9a0f9afs3k2k1fasf9a': 'Example2',
    ...
}

### project id to duration
project_timeids = {
    '890dfafasfd98dfasf908afd': 0, # Example 1
    'faf7a9a0f9afs3k2k1fasf9a': 0, # Example 2
    ...
}

### project id to cells
project_cellids = {
    '890dfafasfd98dfasf908afd': 'C1',
    'faf7a9a0f9afs3k2k1fasf9a': 'C2',
    ...
}

### send request to API
r = requests.get('https://api.clockify.me/api/v1/workspaces/{workspaceId}/user/{User Id}/time-entries', headers=headers, params=payload)
res = r.json()

### iterate through responses to find duration
for responses in res:
    p_id = responses['projectId'] 

    if p_id is None:
        continue
    
    res_time = str(responses['timeInterval']['duration'])

    print(res_time)
    if res_time is None:
        res_time = 0

    res_duration = 0

    ### algorithm for converting json time into minutes

    if res_time.find('H') != -1: #hours
        res_duration += 60*int(res_time[res_time.find('H')-1])
    
    if res_time.find('M') != -1: #minutes
        tmp = res_time.find('M')-2
        if res_time[tmp].isdigit():
            res_duration += 10*int(res_time[tmp])
        res_duration += int(res_time[res_time.find('M')-1])

    if res_time.find('S') != -1: #seconds
        tmp = res_time.find('S')-2
        if res_time[tmp].isdigit():
            res_duration += (10*int(res_time[(res_time.find('S')-2)]))/60   
        res_duration += (int(res_time[(res_time.find('S')-1)]))/60

    project_timeids[p_id] += res_duration

### writing to excel file
xfile = openpyxl.load_workbook('file_name.xlsx')

sheet = xfile['sheet_name1']

nums_of_projects = 0
for cell_id in project_cellids:
    sheet[project_cellids[cell_id]].value = project_timeids[cell_id]
    nums_of_projects += 1

xfile.save('file_name.xlsx')

### formula for converting into score
score = 0
def convert(optimal, actual, weight, overunder):
    if overunder == 'Under':
        if actual >= optimal:
            return weight
        else:
            return weight*(actual/optimal)
    elif overunder == 'Over':
        if optimal >= actual:
            return weight
        else:
            return weight*(optimal/actual)

### iterate through all rows
sum_of_weights = 0
for row in range(2, 2 + nums_of_projects):
    optimal = sheet['B' + str(row)].value
    actual = sheet['C' + str(row)].value
    weight = sheet['D' + str(row)].value
    sum_of_weights += weight
    overunder = sheet['E' + str(row)].value
    score += convert(optimal, actual, weight, overunder)

### calculate grade, can be modified. you can also call it as a function but I decided to do inline for simplicity of flow
s = score/sum_of_weights
if s >= 0.97:
    grade = 'A+'
elif s >= 0.93:
    grade = 'A'
elif s >= 0.9:
    grade = 'A-'
elif s >= 0.87:
    grade = 'B+'
elif s >= 0.83:
    grade = 'B'
elif s >= 0.8:
    grade = 'B-'
elif s >= 0.77:
    grade = 'C+'
elif s >= 0.73:
    grade = 'C'
elif s >= 0.70:
    grade = 'C-'
elif s >= 0.6:
    grade = 'D'
else:
    grade = 'F'

### write the score into the excel sheet history
yfile = openpyxl.load_workbook('file_name.xlsx')

sheet1 = yfile['sheet_name2']

d0 = date(2020, 6, 28)
d1 = date.today()
delta = d1-d0
cell_num = delta.days + 2

sheet1['A' + str(cell_num)].value = date.today()
sheet1['B' + str(cell_num)].value = str(round(score,2)) + " out of " + str(sum_of_weights)
sheet1['C' + str(cell_num)].value = str(grade)

yfile.save('PersonalAccountability.xlsx')

### discord implementation
webhook = DiscordWebhook('your_discord_webhook')

embed = DiscordEmbed(title='Score', description=str(round(score,2)) + " out of " + str(sum_of_weights) + " | " + grade, color=242424)
embed.set_author(name='Accountability Report', icon_url='https://images-na.ssl-images-amazon.com/images/I/812L5zyAmpL._AC_SL1500_.jpg')

for proj_id in project_nameids:
    if proj_id in project_cellids:
        cell = project_cellids[proj_id].replace('C', 'B')
        optimal = sheet[cell].value
    else:
        optimal = "N/A"

    ### embeded info
    embed.add_embed_field(name=project_nameids[proj_id], value="Actual: " + str(round(project_timeids[proj_id], 2)) + 
                    " mins" + "\n" + "Expected: " + str(optimal) + " mins" + "\n" + "% Diff: " + 
                    (str(round(100*project_timeids[proj_id]/optimal)) if optimal != "N/A" else "N/A") + "%")

webhook.add_embed(embed)

webhook.execute()
