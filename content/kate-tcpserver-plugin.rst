TCP Server Plugin for Kate
==========================

:date: 2013-03-09
:tags: linux, kde, Qt


I've been working on Kate for some time now. As I mentioned before, I
was trying to make a TCP Server plugin for Kate, where there's a simple
TCP server running on some port, and data recieved on that port is
entered into the editor. I finally got it to work the day before. There
are a few intricacies about this and I'll point those out here, for
everyone's benefit (including my own!).

Before I get down to explaining the code, I would like to clarify that
this is not really a Kate plugin. It's a plugin for 'KTextEditor' which
is a pluggable interface you can use in your own code. So this plugin
is functional in both Kwrite and Kate. (In Kate, it'll show up under
'Extensions', not 'Plugins'.) Also, when I wrote this plugin, I was
following the `excellent tutorial on the KDE Techbase
<https://techbase.kde.org/Development/Tutorials/Kate/KTextEditor_Plugins>`_,
so what I've done is a kind of extension of the plugin described there.

Every KTextEditor plugin needs a '.rc' file for the UI, a '.desktop'
file where some generic information about the plugin is specified.
There's a CMakeLists.txt file which specifies how you want the plugin
to be compiled and takes care of a few other things, such as moving
the '.rc' and '.desktop' files to the appropriate places. We'll use
the following files:


* Desktop File: `ktexteditor-tcpserver.desktop <https://github.com/czardoz/ktexteditor-tcpserver-plugin/blob/master/ktexteditor-tcpserver.desktop>`_
* UI File: `tcpserverui.rc <https://github.com/czardoz/ktexteditor-tcpserver-plugin/blob/master/tcpserverui.rc>`_
* Header File: `tcpserver.hpp <https://github.com/czardoz/ktexteditor-tcpserver-plugin/blob/master/tcpserver.hpp>`_
* Implementation File: `tcpserver.cpp <https://github.com/czardoz/ktexteditor-tcpserver-plugin/blob/master/tcpserver.cpp>`_
* CMakeLists: `CMakeLists.txt <https://github.com/czardoz/ktexteditor-tcpserver-plugin/blob/master/CMakeLists.txt>`_

It's best to start with the '.desktop' file, since it contains the
name of the plugin (as well as the author!) and other important information.

.. code-block:: text

    ###
    ##     Desktop Configuration File
    #

    [Desktop Entry]
    Encoding=UTF-8
    X-KDE-Library=ktexteditor-tcpserver
    X-KDE-PluginInfo-Author=Aniket Panse
    X-KDE-PluginInfo-Email=contact@aniketpanse.in
    X-KDE-PluginInfo-Name=ktexteditortcpserver
    X-KDE-PluginInfo-Version=0.1
    X-KDE-PluginInfo-Website=http://kate.kde.org
    X-KDE-PluginInfo-Category=Editor
    X-KDE-PluginInfo-Depends=
    X-KDE-PluginInfo-License=GPL
    X-KDE-PluginInfo-EnabledByDefault=false
    X-KDE-ParentApp=kate
    X-KDE-Version=4.0
    ServiceTypes=KTextEditor/Plugin
    Type=Service
    Icon=korganizer
    Name=TCP Server
    Comment=Inserts data from a TCP socket.

Since all these have been explained in the original tutorial, I won't go
into the details. But I'll mention one point. The `X-KDE-Library` value
absolutely has to be the name of the library that you're going to build.
This library name is also used in CMakeLists.txt as we'll see. Here's the
CMakeLists.txt file which we'll use:

.. code-block:: cmake

    ###
    ##     CMakeLists.txt
    #


    #We need KDE 4
    find_package(KDE4 REQUIRED)

    include(KDE4Defaults)
    include_directories(${KDE4_INCLUDES})

    kde4_add_plugin(ktexteditor-tcpserver tcpserver.cpp)

    #   Here's what I was referring to before. The first argument HAS to match
    #   the library name we defined in the desktop configuration file.
    target_link_libraries(ktexteditor-tcpserver ${KDE4_KDECORE_LIBS} ${KDE4_KDEUI_LIBS} ktexteditor)

    #   Build the library
    install(TARGETS ktexteditor-tcpserver DESTINATION ${PLUGIN_INSTALL_DIR})

    #   Copy the useful files into the file system
    install(FILES tcpserverui.rc DESTINATION ${DATA_INSTALL_DIR}/ktexteditor-tcpserver)
    install(FILES ktexteditor-tcpserver.desktop DESTINATION ${SERVICES_INSTALL_DIR})

Notice how the library name we entered (`ktexteditor-tcpserver`) is being used here. Also, you can
see how the User Interface file (tcpserverui.rc) and the Desktop
Configuration File are being copied to the file system. (In practice,
they end up in /usr/share/apps/<folder-name>/ and /usr/share/kde4/services/
respectively).

Now for the actual code! I'll start by explaining the header file,
since it holds the class definitions. I'll ignore all the includes,
and jump directly to the classes.

.. code-block:: cpp

    class TCPServerPlugin
      : public KTextEditor::Plugin
    {
        public:

            explicit TCPServerPlugin(QObject *parent,
                                     const QVariantList &args);

            virtual ~TCPServerPlugin();
            void addView (KTextEditor::View *view);
            void removeView (KTextEditor::View *view);

            void readConfig();
            void writeConfig();

        private:
            QList<class TCPServerPluginView*> m_views;
    };

Pretty standard things here. Our main plugin class inherits from the
`KTextEditor::Plugin` class. It overrides the `addView()` and `removeView()`
methods. Since our plugin has no configurable options, we ignore the
other two methods (for now).

Let's look at the TCPServerPluginView class, which is a bit more interesting ;-)

.. code-block:: cpp

    class TCPServerPluginView
        : public QObject, public KXMLGUIClient
    {
        Q_OBJECT

        public:
            explicit TCPServerPluginView(KTextEditor::View* view = 0);
            ~TCPServerPluginView();

        private Q_SLOTS:
            void slotInsertText();
            void slotHandleConnection();
            void slotGetData();
            void slotDisconnected();

        private:
            KTextEditor::View *m_view;
            QTcpServer *srv;
            QTcpSocket *clientSocket;
    };

Here, we have four 'Slots'. Slot is basically a Qt concept, and is an
alternative to callbacks. The concept is simple, you can emit 'Signals'
from a Class. Each signal may be connected to one or more Slots.
Whenever a Signal is emitted, the slot is called, which is why ideally,
slots shouldn't use any special types. You can read more about this `here <http://doc.qt.io/qt-4.8/signalsandslots.html>`_.
We also have a QTcpServer and a QTcpSocket as members of this Class.
We'll understand their use better when we take a look at the tcpserver.cpp
file. Notice that TCPServerPluginView also inherits from KXMLGUIClient.
That is why we can use the tcpserverui.rc file to specify the GUI of our
plugin (which consists of only a new entry in the 'Tools' menu).
The slot* methods are explained next, along with the tcpserver.cpp file.

In the tcpserver.cpp file, there are two macros at the very beginning.
Their functions are well documented in the original tutorial, so I'll
skip those, and the Constructor and Destructor for the TCPServerPlugin
Class. These are the overridden methods:

.. code-block:: cpp

    void TCPServerPlugin::addView(KTextEditor::View *view)
    {
        TCPServerPluginView *nview = new TCPServerPluginView(view);
        m_views.append(nview);
    }

    void TCPServerPlugin::removeView(KTextEditor::View *view)
    {
        for(int z = 0; z < m_views.size(); z++)
        {
            if (m_views.at(z)->parentClient() == view)
            {
                TCPServerPluginView *nview = m_views.at(z);
                m_views.removeAll(nview);
                delete nview;
            }
        }
    }

The addView() method basically adds a new TCPServerPluginView object
to our TCPServerPlugin. Now, each TCPServerPluginView has a m_view member,
which is a KTextEditor::View. The 'View' provides the graphical representation
of a KTextEditor::Document, as said in the API Reference Each plugin may
have many views. For us, since the QTcpServer object is a member of the
TCPServerPluginView, we can have only one TCPServerPluginView, otherwise
the multiple TCPServerPluginView will all try to listen on the same port
(which is hardcoded for now) and cause a crash. Next is the TCPServerPluginView
constructor:

.. code-block:: cpp

    TCPServerPluginView::TCPServerPluginView(KTextEditor::View *view)
        : QObject(view)
        , KXMLGUIClient(view)
        , m_view(view)
    {
        setComponentData(TCPServerPluginFactory::componentData());

        KAction *action = new KAction(i18n("Insert thing from TCP Socket"), this);
        actionCollection()->addAction("tools_insert_chars", action);
        action->setShortcut(Qt::CTRL + Qt::Key_M);
        connect(action, SIGNAL(triggered()), this, SLOT(slotInsertText()));
        srv = new QTcpServer(this);
        srv->listen(QHostAddress::Any, 3000);
        connect(srv, SIGNAL(newConnection()), this, SLOT(slotHandleConnection()));
        QList<QHostAddress> ipAddressesList = QNetworkInterface::allAddresses();

        for(int i=0; i<ipAddressesList.size(); i++)
        {
            kDebug() << ipAddressesList.at(i).toString();
        }

        setXMLFile("tcpserverui.rc");
    }

Here, we have first initialized our server, and by calling the listen() method,
we are listening on port 3000 (which is the second argument to the method).
The first argument, QHostAddress::Any tells our QTcpServer to listen on all
interfaces. The next call to connect() the the heart of our plugin. This call
connects the Signal newConnection() (emitted by the srv object) to the
slotHandleConnection() method (or 'Slot') of TCPServerPluginView. So whenever
a client connects to the server, slotHandleConnection() is called. I'm just
going to skip the slotInsertText() method, because all it does is insert
"Awesome Server" into the current document at the cursor position. With that,
we come to the slotHandleConnection() method:

.. code-block:: cpp

    void TCPServerPluginView::slotHandleConnection()
    {
        clientSocket = srv->nextPendingConnection();
        connect(clientSocket, SIGNAL(readyRead()), this, SLOT(slotGetData()));
        connect(clientSocket, SIGNAL(disconnected()), clientSocket, SLOT(deleteLater()));
        connect(clientSocket, SIGNAL(disconnected()), this, SLOT(slotDisconnected()));
    }

In this method, we accept the incoming connection which caused
slotHandleConnection() to be called. We also make a few more connections,
so that whenever there's data at the socket which has not been read yet, the
slotGetData() method's called. Also, on disconnect, we need to clear up the
memory allocated to our clientSocket. The slotDisconnected() method is for
debugging only, all it does is print a debug message.

.. code-block:: cpp

    void TCPServerPluginView::slotGetData()
    {
        QByteArray data;
        data = clientSocket->readAll();
        kDebug() << data;
        m_view->document()->insertText(m_view->cursorPosition(), QString(data));
    }

    void TCPServerPluginView::slotDisconnected()
    {
        kDebug() << "Socket Disconnected, ready for new connection";
    }

The slotGetData() method accepts the data from the socket, and reads it into
a QByteArray. This data is then inserted into our document, by calling it's
insertText() method. Also, the cursor position, which is handled by
KTextEditor::View object m_view is passed as the first argument, to specify
where the text is to be inserted. Now let's have a look at the final piece of
the puzzle, tcpserverui.rc:

.. code-block:: xml

    <!-- tcpserverui.rc -->
    <!DOCTYPE kpartgui>
    <kpartplugin name="ktexteditor-tcpserver" library="ktexteditor-tcpserver" version="2">
    <MenuBar>
     <Menu name="tools"><Text>&Tools</Text>
        <separator group="tools_operations" />
        <Action name="tools_insert_chars" group="tools_operations"/>
     </Menu>
    </MenuBar>
    </kpartplugin>

Here we define a GUI to our plugin. Since the main Kate environment is also
specified by KXMLGUIWindow, or KXMLGUIClient classes, this file will allow the
GUI for our plugin to be 'inserted' into the Kate GUI. Also, we have an action
defined here, tools_insert_chars. If you remember, this action was used in the
constructor for our TCPServerPluginView class, and it was assigned the shortcut
Ctrl+M. This action triggers our slotInsertText() method, which causes "Awesome
Server" to be inserted into the current document.

That's about all there is to this plugin... You can find the whole code
`on GitHub <https://github.com/czardoz/ktexteditor-tcpserver-plugin>`_.
I will try to write another part to this, where we're allowed to configure the
port where we want to listen (currently it is hardcoded to 3000).
