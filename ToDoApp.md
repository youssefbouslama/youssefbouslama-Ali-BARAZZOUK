#  ToDoApp
##  Overview
The main objective of this project is to make an application that can manage our own tasks containing actions and menues ... 
![enter image description here](https://cdn.discordapp.com/attachments/717289423930130554/934856415502303262/unknown.png)
## Interface
We will be starting by desining this application  using QT Designer
![enter image description here](https://cdn.discordapp.com/attachments/717289423930130554/934856904142880828/unknown.png)
##  Defining a Task
We will need to define the following attributes to the task:
-   A  description: stating the text and goal for the task like (Buying the milk).
    
-   A  finished  boolean indicating if the task is Finished or due.
-   A  tag category to show the class of the task which is reduced to the following values:
    
    -   Work
    -   Life
    -   Other
-   Finally, a task should have a  DueDate  which stores the  Date planned for the date.
We created the dialog with QT Disigner
![enter image description here](https://cdn.discordapp.com/attachments/717289423930130554/934859525800984656/unknown.png)
-  Adding date setter.
```cpp
void addTaskDialog::setdate(int y,int m, int d){
    QDate da;

    da.setDate(y,m,d);
    ui->dateEdit->setDate(da);
}
```
-  Adding description setter.
 ```cpp
 void addTaskDialog::setdesc(QString a){
    ui->lineEdit->setText(a);
}
```
 - Adding tag setter.
```cpp
void addTaskDialog::settag(QString a){
    ui->comboBox->setCurrentText(a);
}
```
- Adding checkbox setter
```cpp
void addTaskDialog::setf(bool a){
    ui->checkBox->setChecked(a);
}
```
- The gettext function
 ```cpp
 QString addTaskDialog::getText(){

    QString a= ui->lineEdit->text() + " Due : " + ui->dateEdit->text() + "  Tag : " + ui->comboBox->currentText() +"";
    return a;
}
```
- To see if  the checkbox checked
```cpp
bool addTaskDialog::isChecked(){
    if(ui->checkBox->isChecked()){
        return true;
    }
    return false;
}
```
- setting a minimum date
```cpp
QDate date =QDate::currentDate();
    ui->dateEdit->setMinimumDate(date);
    ui->dateEdit->setDate(date);
```
![enter image description here](https://cdn.discordapp.com/attachments/717289423930130554/934862676088225792/unknown.png)
we need to add a slot in the header
```cpp
private slots :
  void on_action_Add_triggered();
  ```
 implementing the function by calling the task dialog and saving that task using the gettext function
 ```cpp
 addTaskDialog dialog;
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
```
- to show or hide the pending 
```cpp
private slots:
    void on_action_View_pending_tasks_toggled(bool arg1);
    void on_action_View_finished_tasks_toggled(bool arg1);
```
- implementingon_action_View_finished_tasks_toggled slot
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
- for saving tasks after closing the application and saving as .txt file
```cpp
protected:
    void closeEvent(QCloseEvent* e) override;
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
- to open tasks (we decide where to add this item by checking the first character of the line)
```cpp
      QFile file("C:/Users/zakariae zaoui/Desktop/alo.txt");

      if (!file.open(QIODevice::ReadOnly | QIODevice::Text))
            return;
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
- changing the content of an item and adding thos slots to the header and impementing the slots sss()


```cpp
connect(ui->listWidget, SIGNAL(itemDoubleClicked(QListWidgetItem*)), this,SLOT(sss()));
connect(ui->lw2, SIGNAL(itemDoubleClicked(QListWidgetItem*)), this,SLOT(sss2()));
connect(ui->lw3, SIGNAL(itemDoubleClicked(QListWidgetItem*)), this,SLOT(sss3()));
```
```cpp
private slots:    
    void sss();
    void sss2();
    void sss3();
```
impementing the slots by declaring the new task then splitting the task
then setting the date then the description then the tag
```cpp
void ToDoApp::sss(){
    addTaskDialog dialog;
    int i=1;
    QString task;
    QListWidgetItem *a=ui->listWidget->currentItem();
    QStringList list = a->text().split(QRegularExpression("\\W+"), Qt::SkipEmptyParts);
    task+=list[0];
    while(list[i]!="Due"){
         task+=" "+list[i];
         i++;
    }
    dialog.setdate(list[i+3].toInt(),list[i+1].toInt(),list[i+2].toInt());
    dialog.setdesc(task);
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
         delete a ;
     }
}
```
we do the same thing for the 2nd slot but for the the 3rd we need to change the checkbox status
```cpp
void ToDoApp::sss(){
    addTaskDialog dialog;
    int i=1;
    
    QString task;
    QListWidgetItem *a=ui->listWidget->currentItem();
    QStringList list = a->text().split(QRegularExpression("\\W+"), Qt::SkipEmptyParts);
    task+=list[0];
    while(list[i]!="Due"){
         task+=" "+list[i];
         i++;
    }
    dialog.setdate(list[i+3].toInt(),list[i+1].toInt(),list[i+2].toInt());
    dialog.setdesc(task);
    dialog.setf(true);
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
         delete a ;
     }
}
```
## MVC Model

 weare going to create a form using QListView in QT Designer
 - adding in the header 
 ```cpp
 QStringListModel *model1;
  QStringListModel *model2;
  QStringListModel *model3;
  
  QStringList Todaytasks;
  QStringList Finishedtasks;
  QStringList Pendingtasks;

 ```
 ```cpp
    model1 = new QStringListModel();
    model2 = new QStringListModel();
    model3 = new QStringListModel();
 ```
 - setting the model to the view
	```cpp
	model1->setStringList(Todaytasks);
   ui->lw1->setModel(model1);


   model2->setStringList(Pendingtasks);
   ui->lw2->setModel(model2);


   model3->setStringList(Finishedtasks);
   ui->lw3->setModel(model3);
	```
	- implementation of the save function
```cpp
	void ToDoApp::closeEvent(QCloseEvent* e){
 QFile file("C:/Users/youssefb /Desktop/QSQ.txt");
 if(file.open(QIODevice::ReadWrite | QIODevice::Text)){
     QTextStream out(&file);
     for(int i=0;i<Todaytasks.size();i++)
     {
         out << "1"<< Todaytasks.at(i)<< Qt::endl;
     }
     for(int i=0;i<Pendingtasks.size();i++)
     {
         out << "2"<< Pendingtasks.at(i) << Qt::endl;
     }
     for(int i=0;i<Finishedtasks.size();i++)
     {
         out << "3"<< Finishedtasks.at(i) << Qt::endl;
     }
     file.close();
 }
}
	```
	-implementation of the open function
```cpp
QFile file("C:/Users/youssefb /Desktop/ASQ.txt");

   if (!file.open(QIODevice::ReadOnly | QIODevice::Text))
         return;

   while (!file.atEnd()) {
       QString line = file.readLine();
       if(line.at(0)=="1"){
             Todaytasks.append(line.mid(1,line.size()));
       }else if(line.at(0)=="2"){
           Pendingtasks.append(line.mid(1,line.size()));
       }else if(line.at(0)=="3"){
           Finishedtasks.append(line.mid(1,line.size()));
       }
   }
   
   model1->setStringList(Todaytasks);
   ui->lw1->setModel(model1);

   model2->setStringList(Pendingtasks);
   ui->lw2->setModel(model2);

   model3->setStringList(Finishedtasks);
   ui->lw3->setModel(model3);
```
- implementation of sss()
```cpp
void ToDoApp::sss(){
 addTaskDialog dialog;
 int i=1;
 QString task;
 QModelIndex index=ui->lw1->currentIndex();
 QString a=Todaytasks.at(index.row());
 QStringList list = a.split(QRegularExpression("\\W+"), Qt::SkipEmptyParts);
 task+=list[0];

 while(list[i]!="Due"){
      task+=" "+list[i];
      i++;
 }

 dialog.setdate(list[i+3].toInt(),list[i+1].toInt(),list[i+2].toInt());
 dialog.setdesc(task);
 dialog.settag(list[i+5]);
 auto reply= dialog.exec();


  if(reply == addTaskDialog::Accepted)
  {
      QString text= dialog.getText();
      if(dialog.getDate()==QDate::currentDate() && !dialog.isChecked()){
          Todaytasks.append(text);
          Todaytasks.removeAt(index.row());

      }else if(dialog.getDate()!=QDate::currentDate() && !dialog.isChecked()){
          Pendingtasks.append(text);
          Todaytasks.removeAt(index.row());
      }else if(dialog.isChecked()){
          Finishedtasks.append(text);
          Todaytasks.removeAt(index.row());
      }

  }

  model1->setStringList(Todaytasks);
  ui->lw1->setModel(model1);

  model2->setStringList(Pendingtasks);
  ui->lw2->setModel(model2);


  model3->setStringList(Finishedtasks);
  ui->lw3->setModel(model3);

}
```
- implementation of sss2()
```cpp 
void ToDoApp::sss2 (){
 addTaskDialog dialog;
 int i=1;
 QString task;
 QModelIndex index=ui->lw2->currentIndex();
 QString a=Pendingtasks.at(index.row());
 QStringList list = a.split(QRegularExpression("\\W+"), Qt::SkipEmptyParts);
 task+=list[0];
 while(list[i]!="Due"){
      task+=" "+list[i];
      i++;
 }
 dialog.setdate(list[i+3].toInt(),list[i+1].toInt(),list[i+2].toInt());
 dialog.setdesc(task);
 dialog.settag(list[i+5]);
 auto reply= dialog.exec();
 if(reply == addTaskDialog::Accepted){
      QString text= dialog.getText();
      if(dialog.getDate()==QDate::currentDate() && !dialog.isChecked()){
          Todaytasks.append(text);
          Pendingtasks.removeAt(index.row());
      }else if(dialog.getDate()!=QDate::currentDate() && !dialog.isChecked()){
          Pendingtasks.append(text);
Pendingtasks.removeAt(index.row());
      }else if(dialog.isChecked()){
          Finishedtasks.append(text);
          Pendingtasks.removeAt(index.row());
      }


 }

 model1->setStringList(Todaytasks);
 ui->lw1->setModel(model1);

 model2->setStringList(Pendingtasks);
 ui->lw2->setModel(model2);


 model3->setStringList(Finishedtasks);
 ui->lw3->setModel(model3);
 }
```
implementation of sss3()
 ```cpp
  void ToDoApp::sss3(){
 addTaskDialog dialog;
 int i=1;
 QString task;
 QModelIndex index=ui->lw3->currentIndex();
 QString a=Finishedtasks.at(index.row());
 QStringList list = a.split(QRegularExpression("\\W+"), Qt::SkipEmptyParts);
 task+=list[0];
 while(list[i]!="Due"){
      task+=" "+list[i];
      i++;
 }
 dialog.setdate(list[i+3].toInt(),list[i+1].toInt(),list[i+2].toInt());
 dialog.setdesc(task);
 dialog.setf(true);
 dialog.settag(list[i+5]);
 auto reply= dialog.exec();
 if(reply == addTaskDialog::Accepted){
      QString text= dialog.getText();
      if(dialog.getDate()==QDate::currentDate() && !dialog.isChecked()){
          Todaytasks.append(text);
           Finishedtasks.removeAt(index.row());
      }else if(dialog.getDate()!=QDate::currentDate() && !dialog.isChecked()){
          Pendingtasks.append(text);
           Finishedtasks.removeAt(index.row());
      }else if(dialog.isChecked()){

      }

 }

 model1->setStringList(Todaytasks);
 ui->lw1->setModel(model1);

 model2->setStringList(Pendingtasks);
 ui->lw2->setModel(model2);

 model3->setStringList(Finishedtasks);
 ui->lw3->setModel(model3);
 }

```
- setting the drag and drop mode
```cpp
ui->lw2->setDragDropMode(QAbstractItemView::DragDrop);
ui->lw1->setDragDropMode(QAbstractItemView::DragDrop);
ui->lw3->setDragDropMode(QAbstractItemView::DropOnly);
```
  # END


 



 










