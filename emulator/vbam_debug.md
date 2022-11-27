# GB debug 

## 1 GB debug bt

    gbEmulate (ticksToStop=72000)
    at C:/msys64/home/yy/gba/visualboyadvance-m/src/gb/GB.cpp:4625
    0x00007ff7e879939c in GameArea::OnIdle (this=0x167c20eb960, event=...)
    at C:/msys64/home/yy/gba/visualboyadvance-m/src/wx/panel.cpp:1161
    0x00007ff7e8ca1135 in wxEvtHandler::ProcessEventIfMatchesId(wxEventTableEntryBase const&, wxEvtHandler*, wxEvent&) ()
    0x00007ff7e8ca124c in wxEventHashTable::HandleEvent(wxEvent&, wxEvtHandler*) ()
    0x00007ff7e8ca164b in wxEvtHandler::TryHereOnly(wxEvent&) ()
    0x00007ff7e8ca169a in wxEvtHandler::ProcessEventLocally(wxEvent&) ()
    0x00007ff7e8ca1769 in wxEvtHandler::ProcessEvent(wxEvent&) ()
    0x00007ff7e8ca145b in wxEvtHandler::SafelyProcessEvent(wxEvent&) ()
    0x00007ff7e8ac5063 in wxWindowBase::SendIdleEvents(wxIdleEvent&) ()
    0x00007ff7e8ac5030 in wxWindowBase::SendIdleEvents(wxIdleEvent&) ()
    0x00007ff7e89db37c in wxAppBase::ProcessIdle() ()
    0x00007ff7e8bc2d79 in wxEventLoopManual::DoRun() ()
    0x00007ff7e8bc2b18 in wxEventLoopBase::Run() ()
    0x00007ff7e8b945a0 in wxAppConsoleBase::MainLoop() ()
    0x00007ff7e871ec9c in wxvbamApp::OnRun (this=<optimized out>)
    at C:/msys64/home/yy/gba/visualboyadvance-m/src/wx/wxvbam.cpp:479
    0x00007ff7e8c0491c in wxEntryReal(int&, wchar_t**) ()
    0x00007ff7e88d287d in wxEntry(HINSTANCE__*, HINSTANCE__*, char*, int) ()
    0x00007ff7e8711bda in WinMain (hInstance=<optimized out>,
    hPrevInstance=hPrevInstance@entry=0x0, nCmdShow=nCmdShow@entry=0)
    at C:/msys64/home/yy/gba/visualboyadvance-m/src/wx/wxvbam.cpp:33
    0x00007ff7e8711c60 in main (argc=<optimized out>, argv=<optimized out>)
    at C:/msys64/home/yy/gba/visualboyadvance-m/src/wx/wxvbam.cpp:41
 
## 2 GB start flow

    wx/wxvbam.app:main -> WinMain -> wxvbamApp::OnRun -> wxAppConsoleBase::MainLoop -> wxEventLoopBase::Run ->
    wxEventLoopManual::DoRun -> wxAppBase::ProcessIdle -> wxWindowBase::SendIdleEvents ->
    wxEvtHandler::SafelyProcessEvent -> wxEvtHandler::ProcessEvent -> wxEvtHandler::ProcessEventLocally ->
    wxEvtHandler::TryHereOnly -> wxEventHashTable::HandleEvent -> wxEvtHandler::ProcessEventIfMatchesId ->
    wx/panel.cpp:GameArea::OnIdle -> gb/GB.cpp:gbEmulate

