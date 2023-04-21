import requests
import re
import csv
import smtplib
import pandas as pd
from email.mime.text import MIMEText
from email.header import Header

url = "http://jw1.hu******ua.net/jwglxt/cjcx/cjcx_cxXsgrcj.html?doType=query&gnmkdm=N305005&su=21********92"

header = {
    "Cookie": "JSESSIONID=DE9E2ACB7633F229D963580FAE8B1899; route=ec9f1523782f6fc8f64960f81492aa8e",
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/107.0.0.0 Safari/537.36 Edg/107.0.1418.56"
}

data = {
    "xnm": 2021,
    "xqm": 3,
    "_search": "false",
    "nd": "1669364752823",
    "queryModel.showCount": 15,
    "queryModel.currentPage": 1,
    "queryModel.sortName": " ",
    "queryModel.sortOrder": "asc",
    "time": 2
}

resp = requests.post(url=url, headers=header, data=data)
resp.encoding = "utf-8"
text = resp.json()
print(text)

obj = re.compile(r"'cj': '(?P<score>.*?)'.*?'jsxm': '(?P<teacher>.*?)'.*?'kcmc': '(?P<class>.*?)', 'kcxzdm'", re.S)
result = obj.finditer(str(text))

f = open("Scores.csv", mode="w", encoding="utf-8", newline='')
csvwriter = csv.writer(f)
# 提取信息
for i in result:
    dic = i.groupdict()
    csvwriter.writerow(dic.values())
f.close()

file = "Scores.csv"
df = pd.read_csv(file, engine='python', encoding="utf-8")
turn_html = df.to_html()
print(turn_html)
file_name = "Scores.html"
with open(file_name, "w", encoding="utf-8") as file_object:
    file_object.write(df.to_html())

f1 = open(file_name, mode="rb")
main_body = f1.read()
f1.close()

message = MIMEText(main_body, "html", "utf-8")

# 发件人姓名
message['From'] = Header("**2103***")
# 收件人姓名
message['To'] = Header(" ")
# 邮件标题
subject = "***发来的邮件(学期成绩)"
message['Subject'] = Header(subject, "utf-8")
# 发送方
sender = "299****92@qq.com"
# 接收方
receiver = "24******55@qq.com"
# 使用QQ邮箱服务，发送邮箱
smtpObj = smtplib.SMTP_SSL("smtp.qq.com", 465)
smtpObj.login(sender, 'dfawrmiyaarldfaf')
smtpObj.sendmail(sender, receiver, message.as_string())
smtpObj.quit()

print("邮件发送成功!")
