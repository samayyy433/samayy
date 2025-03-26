import tkinter as tk
from tkinter import *
import os, cv2
import shutil
import csv
import numpy as np
from PIL import ImageTk, Image
import pandas as pd
import datetime
import time
import tkinter.font as font
from PIL import Image
import pyttsx3

# project module
import show_attendance
import takeImage
import trainImage
import automaticAttedance

# engine = pyttsx3.init()
# engine.say("Welcome!")
# engine.say("Please browse through your options..")
# engine.runAndWait()


def text_to_speech(user_text):
    engine = pyttsx3.init()
    engine.say(user_text)
    engine.runAndWait()


haarcasecade_path = "C:\\Users\\ADITYA\\OneDrive\\Desktop\\Attendance-Management-system-using-face-recognition-master\\haarcascade_frontalface_alt.xml"
trainimagelabel_path = (
    "C:\\Users\\ADITYA\\OneDrive\Desktop\Attendance-Management-system-using-face-recognition-master\\TrainingImageLabel\\Trainner.yml"
)
trainimage_path = "C:\\Users\\ADITYA\\OneDrive\\Desktop\\Attendance-Management-system-using-face-recognition-master\\TrainingImage"
studentdetail_path = (
    "C:\\Users\\ADITYA\\OneDrive\Desktop\\Attendance-Management-system-using-face-recognition-master\\StudentDetails\\studentdetails.csv"
)
attendance_path = "C:\\Users\\ADITYA\\OneDrive\\Desktop\\Attendance-Management-system-using-face-recognition-master\\Attendance"


window = Tk()
window.title("Face recognizer")
window.geometry("1280x720")
dialog_title = "QUIT"
dialog_text = "Are you sure want to close?"
window.configure(background="black")


# to destroy screen
def del_sc1():
    sc1.destroy()


# error message for name and no
def err_screen():
    global sc1
    sc1 = tk.Tk()
    sc1.geometry("400x110")
    sc1.iconbitmap("AMS.ico")
    sc1.title("Warning!!")
    sc1.configure(background="black")
    sc1.resizable(0, 0)
    tk.Label(
        sc1,
        text="Enrollment & Name required!!!",
        fg="yellow",
        bg="black",
        font=("times", 20, " bold "),
    ).pack()
    tk.Button(
        sc1,
        text="OK",
        command=del_sc1,
        fg="yellow",
        bg="black",
        width=9,
        height=1,
        activebackground="Red",
        font=("times", 20, " bold "),
    ).place(x=110, y=50)


def testVal(inStr, acttyp):
    if acttyp == "1":  # insert
        if not inStr.isdigit():
            return False
    return True


logo = Image.open("UI_Image/0001.png")
logo = logo.resize((50, 47), Image.LANCZOS)
logo1 = ImageTk.PhotoImage(logo)
titl = tk.Label(window, bg="black", relief=RIDGE, bd=10, font=("arial", 35))
titl.pack(fill=X)
l1 = tk.Label(window, image=logo1, bg="black",)
l1.place(x=470, y=10)

titl = tk.Label(
    window, text="Smart College!!", bg="black", fg="green", font=("arial", 27),
)
titl.place(x=525, y=12)

a = tk.Label(
    window,
    text="Welcome to the Face Recognition Based\nAttendance Management System",
    bg="black",
    fg="yellow",
    bd=10,
    font=("arial", 35),
)
a.pack()

ri = Image.open("UI_Image/register.png")
r = ImageTk.PhotoImage(ri)
label1 = Label(window, image=r)
label1.image = r
label1.place(x=100, y=270)

ai = Image.open("UI_Image/attendance.png")
a = ImageTk.PhotoImage(ai)
label2 = Label(window, image=a)
label2.image = a
label2.place(x=980, y=270)

vi = Image.open("UI_Image/verifyy.png")
v = ImageTk.PhotoImage(vi)
label3 = Label(window, image=v)
label3.image = v
label3.place(x=600, y=270)


def TakeImageUI():
    ImageUI = Tk()
    ImageUI.title("Take Student Image..")
    ImageUI.geometry("780x480")
    ImageUI.configure(background="black")
    ImageUI.resizable(0, 0)
    titl = tk.Label(ImageUI, bg="black", relief=RIDGE, bd=10, font=("arial", 35))
    titl.pack(fill=X)
    # image and title
    titl = tk.Label(
        ImageUI, text="Register Your Face", bg="black", fg="green", font=("arial", 30),
    )
    titl.place(x=270, y=12)

    # heading
    a = tk.Label(
        ImageUI,
        text="Enter the details",
        bg="black",
        fg="yellow",
        bd=10,
        font=("arial", 24),
    )
    a.place(x=280, y=75)

    # ER no
    lbl1 = tk.Label(
        ImageUI,
        text="Enrollment No",
        width=10,
        height=2,
        bg="black",
        fg="yellow",
        bd=5,
        relief=RIDGE,
        font=("times new roman", 12),
    )
    lbl1.place(x=120, y=130)
    txt1 = tk.Entry(
        ImageUI,
        width=17,
        bd=5,
        validate="key",
        bg="black",
        fg="yellow",
        relief=RIDGE,
        font=("times", 25, "bold"),
    )
    txt1.place(x=250, y=130)
    txt1["validatecommand"] = (txt1.register(testVal), "%P", "%d")

    # name
    lbl2 = tk.Label(
        ImageUI,
        text="Name",
        width=10,
        height=2,
        bg="black",
        fg="yellow",
        bd=5,
        relief=RIDGE,
        font=("times new roman", 12),
    )
    lbl2.place(x=120, y=200)
    txt2 = tk.Entry(
        ImageUI,
        width=17,
        bd=5,
        bg="black",
        fg="yellow",
        relief=RIDGE,
        font=("times", 25, "bold"),
    )
    txt2.place(x=250, y=200)

    lbl3 = tk.Label(
        ImageUI,
        text="Notification",
        width=10,
        height=2,
        bg="black",
        fg="yellow",
        bd=5,
        relief=RIDGE,
        font=("times new roman", 12),
    )
    lbl3.place(x=120, y=270)

    message = tk.Label(
        ImageUI,
        text="",
        width=32,
        height=2,
        bd=5,
        bg="black",
        fg="yellow",
        relief=RIDGE,
        font=("times", 12, "bold"),
    )
    message.place(x=250, y=270)

    def take_image():
        l1 = txt1.get()
        l2 = txt2.get()
        takeImage.TakeImage(
            l1,
            l2,
            haarcasecade_path,
            trainimage_path,
            message,
            err_screen,
            text_to_speech,
        )
        txt1.delete(0, "end")
        txt2.delete(0, "end")

    # take Image button
    # image
    takeImg = tk.Button(
        ImageUI,
        text="Take Image",
        command=take_image,
        bd=10,
        font=("times new roman", 18),
        bg="black",
        fg="yellow",
        height=2,
        width=12,
        relief=RIDGE,
    )
    takeImg.place(x=130, y=350)

    def train_image():
        trainImage.TrainImage(
            haarcasecade_path,
            trainimage_path,
            trainimagelabel_path,
            message,
            text_to_speech,
        )

    # train Image function call
    trainImg = tk.Button(
        ImageUI,
        text="Train Image",
        command=train_image,
        bd=10,
        font=("times new roman", 18),
        bg="black",
        fg="yellow",
        height=2,
        width=12,
        relief=RIDGE,
    )
    trainImg.place(x=360, y=350)


r = tk.Button(
    window,
    text="Register a new student",
    command=TakeImageUI,
    bd=10,
    font=("times new roman", 16),
    bg="black",
    fg="yellow",
    height=2,
    width=17,
)
r.place(x=100, y=520)


def automatic_attedance():
    automaticAttedance.subjectChoose(text_to_speech)


r = tk.Button(
    window,
    text="Take Attendance",
    command=automatic_attedance,
    bd=10,
    font=("times new roman", 16),
    bg="black",
    fg="yellow",
    height=2,
    width=17,
)
r.place(x=600, y=520)


def view_attendance():
    show_attendance.subjectchoose(text_to_speech)


r = tk.Button(
    window,
    text="View Attendance",
    command=view_attendance,
    bd=10,
    font=("times new roman", 16),
    bg="black",
    fg="yellow",
    height=2,
    width=17,
)
r.place(x=1000, y=520)
r = tk.Button(
    window,
    text="EXIT",
    bd=10,
    command=quit,
    font=("times new roman", 16),
    bg="black",
    fg="yellow",
    height=2,
    width=17,
)
r.place(x=600, y=660)

window.mainloop()
import pandas as pd
from glob import glob
import os
import tkinter
import csv
import tkinter as tk
from tkinter import *

def subjectchoose(text_to_speech):
    def calculate_attendance():
        Subject = tx.get()
        if Subject=="":
            t='Please enter the subject name.'
            text_to_speech(t)
        os.chdir(
            f"C:\\Users\\ADITYA\\OneDrive\\Desktop\\Attendance-Management-system-using-face-recognition-master\\Attendance\\{Subject}"
        )
        filenames = glob(
            f"C:\\Users\\ADITYA\\OneDrive\\Desktop\\Attendance-Management-system-using-face-recognition-master\\Attendance\\{Subject}\\{Subject}*.csv"
        )
        df = [pd.read_csv(f) for f in filenames]
        newdf = df[0]
        for i in range(1, len(df)):
            newdf = newdf.merge(df[i], how="outer")
        newdf.fillna(0, inplace=True)
        newdf["Attendance"] = 0
        for i in range(len(newdf)):
            newdf["Attendance"].iloc[i] = f"{int(round(pd.to_numeric(newdf.iloc[i, 2:-1], errors='coerce').mean() * 100))}%"
            #newdf.sort_values(by=['Enrollment'],inplace=True)
        newdf.to_csv("attendance.csv", index=False)

        root = tkinter.Tk()
        root.title("Attendance of "+Subject)
        root.configure(background="black")
        cs = f"C:\\Users\\ADITYA\\OneDrive\\Desktop\\Attendance-Management-system-using-face-recognition-master\\Attendance\\{Subject}\\attendance.csv"
        with open(cs) as file:
            reader = csv.reader(file)
            r = 0

            for col in reader:
                c = 0
                for row in col:

                    label = tkinter.Label(
                        root,
                        width=10,
                        height=1,
                        fg="yellow",
                        font=("times", 15, " bold "),
                        bg="black",
                        text=row,
                        relief=tkinter.RIDGE,
                    )
                    label.grid(row=r, column=c)
                    c += 1
                r += 1
        root.mainloop()
        print(newdf)

    subject = Tk()
    # windo.iconbitmap("AMS.ico")
    subject.title("Subject...")
    subject.geometry("580x320")
    subject.resizable(0, 0)
    subject.configure(background="black")
    # subject_logo = Image.open("UI_Image/0004.png")
    # subject_logo = subject_logo.resize((50, 47), Image.ANTIALIAS)
    # subject_logo1 = ImageTk.PhotoImage(subject_logo)
    titl = tk.Label(subject, bg="black", relief=RIDGE, bd=10, font=("arial", 30))
    titl.pack(fill=X)
    # l1 = tk.Label(subject, image=subject_logo1, bg="black",)
    # l1.place(x=100, y=10)
    titl = tk.Label(
        subject,
        text="Which Subject of Attendance?",
        bg="black",
        fg="green",
        font=("arial", 25),
    )
    titl.place(x=100, y=12)

    def Attf():
        sub = tx.get()
        if sub == "":
            t="Please enter the subject name!!!"
            text_to_speech(t)
        else:
            os.startfile(
            f"C:\\Users\\ADITYA\\OneDrive\\Desktop\\Attendance-Management-system-using-face-recognition-master\\Attendance\\{sub}"
            )


    attf = tk.Button(
        subject,
        text="Check Sheets",
        command=Attf,
        bd=7,
        font=("times new roman", 15),
        bg="black",
        fg="yellow",
        height=2,
        width=10,
        relief=RIDGE,
    )
    attf.place(x=360, y=170)

    sub = tk.Label(
        subject,
        text="Enter Subject",
        width=10,
        height=2,
        bg="black",
        fg="yellow",
        bd=5,
        relief=RIDGE,
        font=("times new roman", 15),
    )
    sub.place(x=50, y=100)

    tx = tk.Entry(
        subject,
        width=15,
        bd=5,
        bg="black",
        fg="yellow",
        relief=RIDGE,
        font=("times", 30, "bold"),
    )
    tx.place(x=190, y=100)

    fill_a = tk.Button(
        subject,
        text="View Attendance",
        command=calculate_attendance,
        bd=7,
        font=("times new roman", 15),
        bg="black",
        fg="yellow",
        height=2,
        width=12,
        relief=RIDGE,
    )
    fill_a.place(x=195, y=170)
    subject.mainloop()
    import csv
import os, cv2
import numpy as np
import pandas as pd
import datetime
import time



# take Image of user
def TakeImage(l1, l2, haarcasecade_path, trainimage_path, message, err_screen,text_to_speech):
    if (l1 == "") and (l2==""):
        t='Please Enter the your Enrollment Number and Name.'
        text_to_speech(t)
    elif l1=='':
        t='Please Enter the your Enrollment Number.'
        text_to_speech(t)
    elif l2 == "":
        t='Please Enter the your Name.'
        text_to_speech(t)
    else:
        try:
            cam = cv2.VideoCapture(0)
            detector = cv2.CascadeClassifier(haarcasecade_path)
            Enrollment = l1
            Name = l2
            sampleNum = 0
            directory = Enrollment + "_" + Name
            path = os.path.join(trainimage_path, directory)
            os.mkdir(path)
            while True:
                ret, img = cam.read()
                gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
                faces = detector.detectMultiScale(gray, 1.3, 5)
                for (x, y, w, h) in faces:
                    cv2.rectangle(img, (x, y), (x + w, y + h), (255, 0, 0), 2)
                    sampleNum = sampleNum + 1
                    cv2.imwrite(
                        f"{path}\ "
                        + Name
                        + "_"
                        + Enrollment
                        + "_"
                        + str(sampleNum)
                        + ".jpg",
                        gray[y : y + h, x : x + w],
                    )
                    cv2.imshow("Frame", img)
                if cv2.waitKey(1) & 0xFF == ord("q"):
                    break
                elif sampleNum > 50:
                    break
            cam.release()
            cv2.destroyAllWindows()
            row = [Enrollment, Name]
            with open(
                "C:\\Users\\ADITYA\\OneDrive\\Desktop\\Attendance-Management-system-using-face-recognition-master\\StudentDetails\\studentdetails.csv",
                "a+",
            ) as csvFile:
                writer = csv.writer(csvFile, delimiter=",")
                writer.writerow(row)
                csvFile.close()
            res = "Images Saved for ER No:" + Enrollment + " Name:" + Name
            message.configure(text=res)
            text_to_speech(res)
        except FileExistsError as F:
            F = "Student Data already exists"
            text_to_speech(F)
            import tkinter as tk
from tkinter import Message, Text
import os, cv2
import shutil
import csv
import numpy as np
from PIL import ImageTk, Image
import pandas as pd
import datetime
import time
import tkinter.ttk as tkk
import tkinter.font as font

ts = time.time()
Date = datetime.datetime.fromtimestamp(ts).strftime("%Y_%m_%d")
timeStamp = datetime.datetime.fromtimestamp(ts).strftime("%H:%M:%S")
Time = datetime.datetime.fromtimestamp(ts).strftime("%H:%M:%S")
Hour, Minute, Second = timeStamp.split(":")
d = {}
index = 0
####GUI for manually fill attendance
def manually_fill():
    global sb
    sb = tk.Tk()
    sb.iconbitmap("AMS.ico")
    sb.title("Enter subject name...")
    sb.geometry("580x320")
    sb.configure(background="snow")

    def err_screen_for_subject():
        def ec_delete():
            ec.destroy()

        global ec
        ec = tk.Tk()
        ec.geometry("300x100")
        ec.iconbitmap("AMS.ico")
        ec.title("Warning!!")
        ec.configure(background="snow")
        tk.Label(
            ec,
            text="Please enter subject name!!!",
            fg="red",
            bg="white",
            font=("times", 16, " bold "),
        ).pack()
        tk.Button(
            ec,
            text="OK",
            command=ec_delete,
            fg="black",
            bg="lawn green",
            width=9,
            height=1,
            activebackground="Red",
            font=("times", 15, " bold "),
        ).place(x=90, y=50)

    def fill_attendance():

        ##Create table for Attendance
        global subb
        subb = SUB_ENTRY.get()

        if subb == "":
            err_screen_for_subject()
        else:
            sb.destroy()
            MFW = tk.Tk()
            MFW.iconbitmap("AMS.ico")
            MFW.title("Manually attendance of " + str(subb))
            MFW.geometry("880x470")
            MFW.configure(background="snow")

            def del_errsc2():
                errsc2.destroy()

            def err_screen1():
                global errsc2
                errsc2 = tk.Tk()
                errsc2.geometry("330x100")
                errsc2.iconbitmap("AMS.ico")
                errsc2.title("Warning!!")
                errsc2.configure(background="snow")
                tk.Label(
                    errsc2,
                    text="Please enter Student & Enrollment!!!",
                    fg="red",
                    bg="white",
                    font=("times", 16, " bold "),
                ).pack()
                tk.Button(
                    errsc2,
                    text="OK",
                    command=del_errsc2,
                    fg="black",
                    bg="lawn green",
                    width=9,
                    height=1,
                    activebackground="Red",
                    font=("times", 15, " bold "),
                ).place(x=90, y=50)

            def testVal(inStr, acttyp):
                if acttyp == "1":  # insert
                    if not inStr.isdigit():
                        return False
                return True

            ENR = tk.Label(
                MFW,
                text="Enter Enrollment",
                width=15,
                height=2,
                fg="white",
                bg="blue2",
                font=("times", 15, " bold "),
            )
            ENR.place(x=30, y=100)

            STU_NAME = tk.Label(
                MFW,
                text="Enter Student name",
                width=15,
                height=2,
                fg="white",
                bg="blue2",
                font=("times", 15, " bold "),
            )
            STU_NAME.place(x=30, y=200)

            global ENR_ENTRY
            ENR_ENTRY = tk.Entry(
                MFW,
                width=20,
                validate="key",
                bg="yellow",
                fg="red",
                font=("times", 23, " bold "),
            )
            ENR_ENTRY["validatecommand"] = (ENR_ENTRY.register(testVal), "%P", "%d")
            ENR_ENTRY.place(x=290, y=105)

            def remove_enr():
                ENR_ENTRY.delete(first=0, last=22)

            STUDENT_ENTRY = tk.Entry(
                MFW, width=20, bg="yellow", fg="red", font=("times", 23, " bold ")
            )
            STUDENT_ENTRY.place(x=290, y=205)

            def remove_student():
                STUDENT_ENTRY.delete(first=0, last=22)

            ####get important variable

            def enter_data_DB():
                global index
                global d
                ENROLLMENT = ENR_ENTRY.get()
                STUDENT = STUDENT_ENTRY.get()
                if ENROLLMENT == "":
                    err_screen1()
                elif STUDENT == "":
                    err_screen1()
                else:
                    if index == 0:
                        d = {
                            index: {"Enrollment": ENROLLMENT, "Name": STUDENT, Date: 1}
                        }
                        index += 1
                        ENR_ENTRY.delete(0, "end")
                        STUDENT_ENTRY.delete(0, "end")
                    else:
                        d[index] = {"Enrollment": ENROLLMENT, "Name": STUDENT, Date: 1}
                        index += 1
                        ENR_ENTRY.delete(0, "end")
                        STUDENT_ENTRY.delete(0, "end")
                    # TODO implement CSV code
                print(d)

            def create_csv():
                df = pd.DataFrame(d)
                csv_name = (
                    "C:\\Users\\ADITYA\\OneDrive\\Desktop\\Attendance-Management-system-using-face-recognition-master\\Attendance(Manually)\\"
                    + subb
                    + "_"
                    + Date
                    + "_"
                    + Hour
                    + "-"
                    + Minute
                    + "-"
                    + Second
                    + ".csv"
                )
                df.to_csv(csv_name)
                O = "CSV created Successfully"
                Notifi.configure(
                    text=O,
                    bg="Green",
                    fg="white",
                    width=33,
                    font=("times", 19, "bold"),
                )
                Notifi.place(x=180, y=380)
                """import csv
                import tkinter

                root = tkinter.Tk()
                root.title("Attendance of " + subb)
                root.configure(background="snow")
                with open(csv_name, newline="") as file:
                    reader = csv.reader(file)
                    r = 0

                    for col in reader:
                        c = 0
                        for row in col:
                            # i've added some styling
                            label = tkinter.Label(
                                root,
                                width=13,
                                height=1,
                                fg="black",
                                font=("times", 13, " bold "),
                                bg="lawn green",
                                text=row,
                                relief=tkinter.RIDGE,
                            )
                            label.grid(row=r, column=c)
                            c += 1
                        r += 1
                root.mainloop()"""

            Notifi = tk.Label(
                MFW,
                text="CSV created Successfully",
                bg="Green",
                fg="white",
                width=33,
                height=2,
                font=("times", 19, "bold"),
            )

            c1ear_enroll = tk.Button(
                MFW,
                text="Clear",
                command=remove_enr,
                fg="black",
                bg="deep pink",
                width=10,
                height=1,
                activebackground="Red",
                font=("times", 15, " bold "),
            )
            c1ear_enroll.place(x=690, y=100)

            c1ear_student = tk.Button(
                MFW,
                text="Clear",
                command=remove_student,
                fg="black",
                bg="deep pink",
                width=10,
                height=1,
                activebackground="Red",
                font=("times", 15, " bold "),
            )
            c1ear_student.place(x=690, y=200)

            DATA_SUB = tk.Button(
                MFW,
                text="Enter Data",
                command=enter_data_DB,
                fg="black",
                bg="lime green",
                width=20,
                height=2,
                activebackground="Red",
                font=("times", 15, " bold "),
            )
            DATA_SUB.place(x=170, y=300)

            MAKE_CSV = tk.Button(
                MFW,
                text="Convert to CSV",
                command=create_csv,
                fg="black",
                bg="red",
                width=20,
                height=2,
                activebackground="Red",
                font=("times", 15, " bold "),
            )
            MAKE_CSV.place(x=570, y=300)
            # TODO remove check sheet
            def attf():
                import subprocess

                subprocess.Popen(
                    r'explorer /select,"C:\Users\ADITYA\OneDrive\Desktop\Attendance-Management-system-using-face-recognition-master\Attendance(Manually)"'
                )

            attf = tk.Button(
                MFW,
                text="Check Sheets",
                command=attf,
                fg="black",
                bg="lawn green",
                width=12,
                height=1,
                activebackground="Red",
                font=("times", 14, " bold "),
            )
            attf.place(x=730, y=410)

            MFW.mainloop()

    SUB = tk.Label(
        sb,
        text="Enter Subject",
        width=15,
        height=2,
        fg="white",
        bg="blue2",
        font=("times", 15, " bold "),
    )
    SUB.place(x=30, y=100)

    global SUB_ENTRY

    SUB_ENTRY = tk.Entry(
        sb, width=20, bg="yellow", fg="red", font=("times", 23, " bold ")
    )
    SUB_ENTRY.place(x=250, y=105)

    fill_manual_attendance = tk.Button(
        sb,
        text="Fill Attendance",
        command=fill_attendance,
        fg="white",
        bg="deep pink",
        width=20,
        height=2,
        activebackground="Red",
        font=("times", 15, " bold "),
    )
    fill_manual_attendance.place(x=250, y=160)
    sb.mainloop()
    import requests
import cv2
import numpy as np

url = "http://192.168.0.6:8080/shot.jpg"

while True:
    cam = requests.get(url)
    imgNp = np.array(bytearray(cam.content), dtype=np.uint8)
    img = cv2.imdecode(imgNp, -1)
    cv2.imshow("cam", img)

    if cv2.waitKey(1) & 0xFF == ord("q"):
        break
        import csv
import os, cv2
import numpy as np
import pandas as pd
import datetime
import time
from PIL import ImageTk, Image
import cv2 as cv

# Train Image
def TrainImage(haarcasecade_path, trainimage_path, trainimagelabel_path, message,text_to_speech):
    recognizer = cv2.face.LBPHFaceRecognizer_create()
    detector = cv2.CascadeClassifier(haarcasecade_path)
    faces, Id = getImagesAndLables(trainimage_path)
    recognizer.train(faces, np.array(Id))
    recognizer.save(trainimagelabel_path)
    res = "Image Trained successfully"  # +",".join(str(f) for f in Id)
    message.configure(text=res)
    text_to_speech(res)


def getImagesAndLables(path):
    # imagePath = [os.path.join(path, f) for d in os.listdir(path) for f in d]
    newdir = [os.path.join(path, d) for d in os.listdir(path)]
    imagePath = [
        os.path.join(newdir[i], f)
        for i in range(len(newdir))
        for f in os.listdir(newdir[i])
    ]
    faces = []
    Ids = []
    for imagePath in imagePath:
        pilImage = Image.open(imagePath).convert("L")
        imageNp = np.array(pilImage, "uint8")
        Id = int(os.path.split(imagePath)[-1].split("_")[1])
        faces.append(imageNp)
        Ids.append(Id)
    return faces, Ids
