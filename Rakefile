require 'nokogiri'

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

    index "require", "Function", "API-Reference.html#function-require"
    index "Cookie", "Class", "API-Reference.html#cookie-object"
    index "phantomjs", "Command", "API-Reference.html#head"

    docdir = "PhantomJS.docset/Contents/Resources/Documents"
    Dir.glob("#{docdir}/A{PI,pi}-{R,r}eference-*").each do |file|
      name = file.match(/api-reference-(.*?)\.html/i){|m| m[1] }
      path = File.basename(file)

      if name == "phantom"
        index name, "Object", "#{path}#object-#{name.downcase}"
      else
        index name, "Class", "#{path}#module-#{name.downcase}"
      end

      parsed = Nokogiri::HTML(File.read(file))
      [%w(Properties Property), %w(Functions Function), %w(Callbacks Callback)].each do |type, singular|
        node = parsed.xpath("//h3[text()='#{type}']")
        if node.any?
          puts "In #{path} found #{type}"
        end
      end
    end
  end

  task :generate => [:download, :index]
end

task :default => "doc:generate"
