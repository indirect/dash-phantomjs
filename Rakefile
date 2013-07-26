namespace :doc do
  task :download do
    sh "wget -m -p -c -k --user-agent= -e robots=off --wait 1 -E -np " +
      "-R'_history,_new' -nH --cut-dirs=2 " +
      "https://github.com/ariya/phantomjs/wiki/API-Reference " +
      "https://github.com/ariya/phantomjs/wiki/"
    mkdir_p "PhantomJS.docset/Contents/Resources"
    mv "wiki", "PhantomJS.docset/Contents/Resources/Documents"
    cp "icon.png", "PhantomJS.docset"
    cp "Info.plist", "PhantomJS.docset/Contents"
  end

  task :index do
    def db
      'PhantomJS.docset/Contents/Resources/docSet.dsidx'
    end

    def sql(*cmd)
      IO.popen("sqlite3 #{db}", 'w+'){|sql| cmd.each{|l| sql << l } }
    end

    def index(name, type, path)
      sql("INSERT OR IGNORE INTO searchIndex(name, type, path) VALUES ('#{name}', '#{type}', '#{path}');")
    end

    # Create the database and schema
    rm_rf db if File.exist?(db)
    sql "CREATE TABLE searchIndex(id INTEGER PRIMARY KEY, name TEXT, type TEXT, path TEXT);",
      "CREATE UNIQUE INDEX anchor ON searchIndex (name, type, path);"

    index "phantom", "Object", "API-Reference-phantom.html#object-phantom"
  end

  task :generate => [:download, :index]
end

task :default => "doc:generate"
