---
layout: post
title: "시스템트레이딩 챗봇 제작기(1)"
date: 2018-06-17
tags: [주식,챗봇,시스템트레이딩,PyQt5]
excerpt: ""
comments: true
---
# 시스템트레이딩 챗봇 제작기(1)

## 개요

최근에 있던 코인사태를 겪고 난 이후, 나는 **돈**에 아주 많은 관심이 생겼다. 가끔씩 힘들때마다 `어떻게 하면 더 효율적이고 편하게 벌 수 있을까?` 라는 고민이 내 머릿속으로 맴돈다. 또 이럴때만 잔머리가 번쩍이는 나란 인간... 잡소리 각설하고 나를 대신해서 돈을 벌어다주는 기계를 만들어, 오랜 나의 꿈인 행복한 백수를 실현시킬 계획을 짜보았다. 실체가 없는 것은 코인과 비슷하지만, 그 자체로 오랜 기간 가치를 많은 사람에게 인정받고 있는 주식을 목표로 삼았다.

첫번째로 주식을 내가 설정한 투자전략에 맞게 원하는 낮은 가격에 매수하고 목표가에 매도해주는 자동 시스템트레이딩 만들기, 두번째는 투자전략에 맞게 설정한 주식들을 실시간으로 알려주는 챗봇 만들기, 그외 여러가지가 있겠지만 일단 두가지 계획부터 완성시켜 볼 생각이다.

## 사전 준비

일단 주식을 매매할 수 있는 증권사 중 API를 제공해주는 키움증권, 대신증권, 이베스트가 있다. 최종 계획은 이 세가지 전부 통합한 솔루션을 제공하겠지만, 처음은 키움증권 API를 이용하여 만들어보자. 추후에 따로 상세한 설명을 담은 사전 준비 글을 준비할 예정이다.

1. 은행 방문하여 키움증권 계좌 개설
2. [키움증권 홈페이지 계정 생성](https://m.blog.naver.com/PostView.nhn?blogId=eun941&logNo=220508269858&proxyReferer=https%3A%2F%2Fwww.google.co.kr%2F)
3. [키움증권 API 신청](http://qwooowp.blogspot.com/2017/03/openapi.html)
4. [anaconda 32bit 설치](https://www.anaconda.com/download/#windows)
5. [pycharm 설치](https://www.jetbrains.com/pycharm/download/)

## 키움증권 API을 활용한 로그인

키움증권 API를 사용하려면 맨 먼저 증권사 서버에 로그인하는 것이 첫번째 순서이다. 파이썬의 PyQt 또는 자바스크립트의 Electron와 같은 크로스 플랫폼을 이용하거나 C#으로 윈도우 개발해도 상관없다. 요즘 텐서플로우로 인해 파이썬에 관심이 생겨 이번 제작은 파이썬의 PyQt를 이용하였다.

### PyQt
시작하기 전, PyQt가 어떤 라이브러리인지 간단하게 소개할 겸 따로 찾아볼 수 있게 몇가지 블로그 링크를 정리하였다.

PyQt는 임베디드 개발자에게 인기많은 C++ 개발 프레임워크인 QT를 파이썬 바인딩 한 것이다. PyQt를 이용하면 Windows, Linux, MacOS와 같은 운영체제에 제한받지 않고 개발을 진행하여 릴리즈할 수 있다. 현재 공식적으로 배포하는 메이져 버전은 Qt4, Qt5가 있다. 필자는 Qt5를 이용하여 개발하였다.

- [예제로 배우는 PyQt](https://opentutorials.org/module/544)
- [PyQt 공식 문서](https://www.tutorialspoint.com/pyqt/index.htm)
- [파이썬스팟의 PyQt](https://pythonspot.com/pyqt5/)

### 기본 프로젝트 구조
이번 프로젝트는 MVC패턴을 지니고 있어서 개별적으로 역할을 담당할 수 있게 설계할 예정이다.
system_trading_01

### Snap01: login
main.py에서 viewController와 dataModels을 연결하며 앱 실행을 관여한다.
```python
import sys
import viewController as vc
import dataModels as dm
#pyqt

from PyQt5.QtWidgets import *

class MyTradeChatBotApp():
    def __init__(self):
        print("자동매매 프로그램 클래스 입니다")
        self.myDataModel = dm.DataModel()
        self.myViewController = vc.ViewController(self.myDataModel)

if __name__ == "__main__":
    print("메인이 실행되었습니다.")
    app = QApplication(sys.argv)
    myApp = MyTradeChatBotApp()
    myApp.myViewController.show()
    app.exec_()
 ```

viewController.py는 QtDesigner에서 제작한 UI를 불러온 후, 윈도우 레지스터에 등록된 `KHOPENAPI.KHOpenAPICtrl.1` 연결한다. 그리고 KOA studio에 등록된 로그인 관련 API중 로그인 상태와 로그인 이후 받아오는 유저 정보까지 연결하였다.

```python
import dataModels as dm
#pyqt
from PyQt5.QtWidgets import *
from PyQt5 import uic
from PyQt5.QAxContainer import *
from PyQt5.QtGui import *

form_class = uic.loadUiType("main_windows.ui")[0]

class ViewController(QMainWindow, form_class):
    def __init__(self, m_model):
        super().__init__()
        self.setUI()
        self.myModel = m_model
        print("뷰 컨트롤러 입니다")

        # kiwoom Open API
        self.kiwoom = QAxWidget("KHOPENAPI.KHOpenAPICtrl.1")
        self.login()

        # kiwoom Open API Event Trigger
        self.kiwoom.OnEventConnect.connect(self.event_connect)

    def login(self):
        self.kiwoom.dynamicCall("CommConnect()")

    # kiwoom Open API Event Trigger
    def event_connect(self, nErrCode):
        try:
            if nErrCode == 0:
                self.statusbar.showMessage("로그인 성공")
                self.get_login_info()
            elif nErrCode == 100:
                self.statusbar.showMessage("사용자 정보교환 실패")
            elif nErrCode == 101:
                self.statusbar.showMessage("서버접속 실패")
            elif nErrCode == 102:
                self.statusbar.showMessage("버전처리 실패")
        except Exception as e:
            print(e)

    def get_login_info(self):
        accountCount = self.kiwoom.dynamicCall("GetLoginInfo(Qstring)", "ACCOUNT_CNT")
        accountList = self.kiwoom.dynamicCall("GetLoginInfo(Qstring)", "ACCLIST")
        userId = self.kiwoom.dynamicCall("GetLoginInfo(Qstring)", "USER_ID")
        userName = self.kiwoom.dynamicCall("GetLoginInfo(Qstring)", "USER_NAME")
        keyBSEC = self.kiwoom.dynamicCall("GetLoginInfo(Qstring)", "KEY_BSECGB")
        firew = self.kiwoom.dynamicCall("GetLoginInfo(Qstring)", "FIREW_SECGB")
        serverKinds = self.kiwoom.dynamicCall("GetLoginInfo(Qstring)", "GetServerGubun")

        self.myModel.myLoginInfo = dm.DataModel.LoginInfo(accountCount, accountList, userId, userName, keyBSEC, firew, serverKinds)

    def setUI(self):
        self.setupUi(self)
```

dataModels은 말 그대로 데이터 저장하고 main.py에 저장된 데이터를 넘겨주는 센터같은 역할을 담당한다.
```python
class DataModel:
    def __init__(self):
        print("데이터 모델 입니다.")
        myLoginInfo = None
    class LoginInfo:
        def __init__(self, accountCount, accountList, userId, userName, keyBSEC, firew, serverKinds):
            self.accountCount = accountCount
            self.accountList = accountList
            self.userId = userId
            self.userName = userName
            self.keyBSEC = keyBSEC
            self.firew = firew
            self.serverKinds = serverKinds

        def getServerKinds(self):
            if self.serverKinds == str(1):
                return "모의투자"
            else:
                return "실서버"
```

