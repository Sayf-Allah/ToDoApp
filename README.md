# ToDoApp
# Table of Content  
- [Overview](#Overview)
- [the application interface](#the-application-interface)
- [Defining a Task](#Defining-a-Tasks)
- [Add task function](#Add-task-function)
- [hide/show the pending and finished views](#hide/show-the-pending-and-finished-views)
- [Save tasks](#Save-tasks)
- [Open tasks](#Open-tasks)
- [Change the content of an item](#Change-the-content-of-an-item)
- [MVC model](#MVC-model)

  # SpreadSheet
  ## Context
## Overview
The goal of the project is to create an application to manage our tasks. It should have all the features of main application such as menues, actions and toolbar. The application must store an archive of all the pending and finished tasks.
![Capture d’écran 2022-01-16 153918](https://user-images.githubusercontent.com/85891554/150104132-af62f444-93e2-4fa8-a688-73cea8c5885e.png)
## the application interface
first we are going to use Qt Designer to design our application
![Capture d’écran 2022-01-19 104115](https://user-images.githubusercontent.com/85891554/150106421-62b65e20-a967-4d48-bcff-3a741377d1b5.png)
## Defining a Task
now we need to create a dialog on wich we are going to define the tasks
A Task is defined by the following attributes:
- A description: stating the text and goal for the task like (Buying the milk).

- A finished boolean indicating if the task is Finished or due.
- A Tag category to show the class of the task which is reduced to the following values:

  - Work
  - Life
  - Other
- Finally, a task should have a DueDate which stores the Date planned for the date.

we used qt designer to create the dialog

![Capture d’écran 2022-01-19 104030](https://user-images.githubusercontent.com/85891554/150106482-5fe645a5-6240-4bca-bcaf-e34ac62e02b8.png)

now , we will add setters and a function to gettext from the dialog
- date setter 
```cpp
void addTaskDialog::setdate(int y,int m, int d){
    QDate da;

    da.setDate(y,m,d);
    ui->dateEdit->setDate(da);
}
```
- description setter
```cpp
void addTaskDialog::setdesc(QString a){
    ui->lineEdit->setText(a);
}
```
- tag setter
```cpp
void addTaskDialog::settag(QString a){
    ui->comboBox->setCurrentText(a);
}
```
- checkbox setter
```cpp
void addTaskDialog::setf(bool a){
    ui->checkBox->setChecked(a);
}
```
- gettext function
```cpp
QString addTaskDialog::getText(){

    QString a= ui->lineEdit->text() + " Due : " + ui->dateEdit->text() + "  Tag : " + ui->comboBox->currentText() +"";
    return a;
}
```
we need a function to check if the checkbox is checked
```cpp
bool addTaskDialog::isChecked(){
    if(ui->checkBox->isChecked()){
        return true;
    }
    return false;
}
```
we need also to set a minimum date
```cpp
    QDate date =QDate::currentDate();
    ui->dateEdit->setMinimumDate(date);
    ui->dateEdit->setDate(date);
```

![Capture d’écran 2022-01-19 105000](https://user-images.githubusercontent.com/85891554/150106494-7d6f89fe-5029-421e-a781-1358315ec68d.png)

## Add task function
first we add a slot to our header
```cpp
private slots :
  void on_action_Add_triggered();
```
now the implementation of our function :
```cpp
     //first we call the task dialog
     addTaskDialog dialog;
     auto reply= dialog.exec();
     if(reply == addTaskDialog::Accepted)
     {
         //we save the task on a text string using the gettext function
         QString text= dialog.getText();
         // now based on the date and the checkbox status we will decide on wich listwidget we are going to add our task as an item
         if(dialog.getDate()==QDate::currentDate() && !dialog.isChecked()){
             ui->listWidget->addItem(text);
         }else if(dialog.getDate()!=QDate::currentDate() && !dialog.isChecked()){
             ui->lw2->addItem(text);
         }else if(dialog.isChecked()){
             ui->lw3->addItem(text);
         }
     }
```
## hide/show the pending and finished views
The user could either hide/show the pending and finished views
for this we add the following slots to the header
```cpp
private slots:
    void on_action_View_pending_tasks_toggled(bool arg1);
    void on_action_View_finished_tasks_toggled(bool arg1);
```
the implementation of on_action_View_pending_tasks_toggled slot
```cpp
void ToDoApp::on_action_View_pending_tasks_toggled(bool arg1)
{
    if(arg1){
        ui->lw2->setVisible(true);
    }else{
        ui->lw2->setVisible(false);
    }
}
```
the implementation of on_action_View_finished_tasks_toggled slot
```cpp
void ToDoApp::on_action_View_finished_tasks_toggled(bool arg1)
{
    if(arg1){
        ui->lw3->setVisible(true);
    }else{
        ui->lw3->setVisible(false);
    }
}
```
we need to add this code to the constructor to make sure that these widgets are invisible by default
```cpp
   ui->lw2->setVisible(false);
   ui->lw3->setVisible(false);
```
## Save tasks
now we neeed to add a function so we can save our tasks after closing our application
to do this we are going to override a close event
```cpp
protected:
    void closeEvent(QCloseEvent* e) override;
```
we are going to save the tasks on a .txt file
```cpp
void ToDoApp::closeEvent(QCloseEvent* e){
    
    QFile file("C:/Users/zakariae zaoui/Desktop/alo.txt");
    if(file.open(QIODevice::ReadWrite | QIODevice::Text)){
        QTextStream out(&file);
        
        for(int i=0;i<ui->listWidget->count();i++)
        {
            out << "1"<< ui->listWidget->item(i)->text() << Qt::endl;
        }
        for(int i=0;i<ui->lw2->count();i++)
        {
            out << "2"<< ui->lw2->item(i)->text() << Qt::endl;
        }
        for(int i=0;i<ui->lw3->count();i++)
        {
            out << "3"<< ui->lw3->item(i)->text() << Qt::endl;
        }
        file.close();
    }
}
```
## Open tasks
the tasks entered to our application must remains in the app in future use
to do this we need to add this code to the constructor 
```cpp
      QFile file("C:/Users/zakariae zaoui/Desktop/alo.txt");

      if (!file.open(QIODevice::ReadOnly | QIODevice::Text))
            return;
      // by checking the first character of line we can decidde where to add this item
      while (!file.atEnd()) {
          QString line = file.readLine();
          if(line.at(0)=="1"){
                ui->listWidget->addItem(line.mid(1,line.size()));
          }else if(line.at(0)=="2"){
              ui->lw2->addItem(line.mid(1,line.size()));
          }else if(line.at(0)=="3"){
              ui->lw3   ->addItem(line.mid(1,line.size()));
          }
      }
```
## Change the content of an item
first we need to connect the list widget to a function that will be executed if an item is double clicked
```cpp
      connect(ui->listWidget, SIGNAL(itemDoubleClicked(QListWidgetItem*)), this,SLOT(sss()));
      connect(ui->lw2, SIGNAL(itemDoubleClicked(QListWidgetItem*)), this,SLOT(sss2()));
      connect(ui->lw3, SIGNAL(itemDoubleClicked(QListWidgetItem*)), this,SLOT(sss3()));
```
we have to add these three SLOTS to the header
```cpp
private slots:    
    void sss();
    void sss2();
    void sss3();
```
now the implementation of the slots
sss():
```cpp
void ToDoApp::sss(){
    addTaskDialog dialog;
    int i=1;
    
    QString task;
    // declare the new task
    QListWidgetItem *a=ui->listWidget->currentItem();
    // now we split the task 
    QStringList list = a->text().split(QRegularExpression("\\W+"), Qt::SkipEmptyParts);
    task+=list[0];
    
    // this loop is going to store the description on a string named task
    while(list[i]!="Due"){
         task+=" "+list[i];
         i++;
    }
    
    // set the date
    dialog.setdate(list[i+3].toInt(),list[i+1].toInt(),list[i+2].toInt());
    
    // set the description
    dialog.setdesc(task);
    
    //set the tag
    dialog.settag(list[i+5]);
    auto reply= dialog.exec();
    if(reply == addTaskDialog::Accepted)
     {
         QString text= dialog.getText();
         if(dialog.getDate()==QDate::currentDate() && !dialog.isChecked()){
             ui->listWidget->addItem(text);
         }else if(dialog.getDate()!=QDate::currentDate() && !dialog.isChecked()){
             ui->lw2->addItem(text);
         }else if(dialog.isChecked()){
             ui->lw3->addItem(text);
         }
         // delete the previous item so we won't have a duplicated one 
         delete a ;
     }
}
```
same thing for the second slot
the implementation of the third slot is different
we have to change the checkbox status
```cpp
void ToDoApp::sss(){
    addTaskDialog dialog;
    int i=1;
    
    QString task;
    // declare the new task
    QListWidgetItem *a=ui->listWidget->currentItem();
    // now we split the task 
    QStringList list = a->text().split(QRegularExpression("\\W+"), Qt::SkipEmptyParts);
    task+=list[0];
    
    // this loop is going to store the description on a string named task
    while(list[i]!="Due"){
         task+=" "+list[i];
         i++;
    }
    
    // set the date
    dialog.setdate(list[i+3].toInt(),list[i+1].toInt(),list[i+2].toInt());
    
    // set the description
    dialog.setdesc(task);
    
    //set the check box for finished tasks to true
    dialog.setf(true);
    
    //set the tag
    dialog.settag(list[i+5]);
    auto reply= dialog.exec();
    if(reply == addTaskDialog::Accepted)
     {
         QString text= dialog.getText();
         if(dialog.getDate()==QDate::currentDate() && !dialog.isChecked()){
             ui->listWidget->addItem(text);
         }else if(dialog.getDate()!=QDate::currentDate() && !dialog.isChecked()){
             ui->lw2->addItem(text);
         }else if(dialog.isChecked()){
             ui->lw3->addItem(text);
         }
         // delete the previous item so we won't have a duplicated one 
         delete a ;
     }
}
```
now lets do a test
step 1: create a task

![Capture d’écran 2022-01-20 111722](https://user-images.githubusercontent.com/85891554/150321621-09b5cd87-cfe5-43e3-8b7c-9b890b832b60.png)

step 2: double click on the task

![Capture d’écran 2022-01-20 111514](https://user-images.githubusercontent.com/85891554/150321651-3b559758-7f46-4054-a99b-ffe5b26fa7ba.png)

step 3: change the attributes

![Capture d’écran 2022-01-20 111119](https://user-images.githubusercontent.com/85891554/150321824-b09a54bd-46c6-41c2-8121-34c741772794.png)

step 4: save the changes

![Capture d’écran 2022-01-20 112701](https://user-images.githubusercontent.com/85891554/150321847-825e559e-ad11-46c8-84d0-af118dac4dd2.png)

## MVC model
