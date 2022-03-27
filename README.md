# pythontest1
from PyQt5 import QtCore, QtGui, QtWidgets, Qt

from PyQt5.QtWidgets import *
#
import os
os.environ["CUDA_VISIBLE_DEVICES"] = "-1"
#邮件模块
import smtplib
import email
from email.mime.text import MIMEText
from email.mime.image import MIMEImage
from email.mime.multipart import MIMEMultipart
from email.header import  Header
import openpyxl
from PyQt5.QtCore import *
from  builtins import str
class Ui_MainWindow(QtWidgets.QMainWindow):
    def __init__(self):

        super(Ui_MainWindow,self).__init__()

        self.setupUi(self)

        self.retranslateUi(self)

    def setupUi(self, MainWindow):

        MainWindow.setObjectName("MainWindow")

        MainWindow.resize(386, 127)

        self.centralWidget = QtWidgets.QWidget(MainWindow)

        self.centralWidget.setObjectName("centralWidget")

        self.retranslateUi(MainWindow)

        self.pushButton = QtWidgets.QPushButton(self.centralWidget)

        self.pushButton.setGeometry(QtCore.QRect(190, 90, 75, 23))

        self.pushButton.setObjectName("pushButton")

        self.pushButton.setText("打开")

        MainWindow.setCentralWidget(self.centralWidget)

        QtCore.QMetaObject.connectSlotsByName(MainWindow)

        filePath, openfile_name = QFileDialog.getOpenFileName(self,'选择文件','','Excel files(*.xlsx )')
        print(type(filePath))
        print(openfile_name)
        # 读取工作表
        workbook = openpyxl.load_workbook(filePath)  # 返回一个workbook数据类型
        #workbook = xlrd.open_workbook_xls('openfile_name')
        sheet = workbook.active
        cell = sheet['A']
        k = []
        for i in cell:
            k.append(i.value)
        print(k)
        # 设置域名，发件人邮箱，邮箱授权吗，收件人邮箱
        mail_host = "smtp.163.com"
        mail_sender = "gigi1232022@163.com"
        mail_license = "YPCPSQROLVSGPRDD"
        mail_recivers = k
        mm = MIMEMultipart('related')
        # 设置邮件头部内容
        subject_content = "test"
        mm["From"] = "sender_name<gigi1232022@163.com>"
        Tostr = "receiver_%i_name<%str>"
        toStr = ''
        i = 1
        email = ''
        for j in cell:
            email = 'receiver_' + str(i) + '_name<' + j.value + '>,'
            toStr = toStr + email
            i += 1
        mm["To"] = toStr[:-1]
        mm["Subject"] = Header(subject_content, 'utf-8')
        # 添加正文文本

        body_content = "您好这是测试"
        message_text = MIMEText(body_content, "plain", "utf-8")
        mm.attach(message_text)
        # 添加图片
        image_data = open('data/a.png', 'rb')
        message_image = MIMEImage(image_data.read())
        image_data.close()
        mm.attach(message_image)
        # 添加附件(excel)
        # atta =MIMEText(open('.xlsx','rb').read(),'base64','utf-8')
        # atta["Content-Disposition"]='attachment;filename=.xlsx'

        # stp =smtplib.SMTP(mail_host)
        stp = smtplib.SMTP_SSL(mail_host, 465)
        print(mail_host)
        # stp.connect(mail_host,25)
        stp.set_debuglevel(1)
        # stp.ehlo()
        # stp.starttls()
        stp.login(mail_sender, mail_license)
        stp.sendmail(mail_sender, mail_recivers, mm.as_string())
        print("发送成功")
        stp.quit()
if __name__ == "__main__":
    import sys

    #app = QtWidgets.QApplication(sys.argv)
    app = QApplication(sys.argv)
    MainWindow = QtWidgets.QMainWindow()
    ui = Ui_MainWindow()

    ui.setupUi(MainWindow)

    MainWindow.show()
sys.exit(app.exec_())


