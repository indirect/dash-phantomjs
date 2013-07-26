namespace :doc do
  task :download do
    sh "wget -mpck --user-agent= -e robots=off --wait 1 -E -np https://github.com/ariya/phantomjs/wiki/home"
    mkdir_p "PhantomJS.docset/Contents/Resources"
    mv "github.com/ariya/phantomjs/wiki", "PhantomJS.docset/Contents/Resources/Documents"
    rm_rf "github.com"
    cp "icon.png", "PhantomJS.docset"
    cp "Info.plist", "PhantomJS.docset/Contents"
  end
end
