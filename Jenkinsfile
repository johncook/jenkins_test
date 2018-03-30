pipeline {
    agent any

    stages {
        stage('Prepare') {
            steps {
                sh 'composer install'
                sh 'rm -rf build/api'
                sh 'rm -rf build/coverage'
                sh 'rm -rf build/logs'
                sh 'rm -rf build/pdepend'
                sh 'rm -rf build/phpdox'
                sh 'mkdir -p build/api'
                sh 'mkdir build/coverage'
                sh 'mkdir build/logs'
                sh 'mkdir build/pdepend'
                sh 'mkdir build/phpdox'
                sh 'cd web && ../vendor/bin/drush site-install --verbose --yes --db-url=sqlite://tmp/site.sqlite'
            }
        }
        stage('PHP Syntax check') {
            steps {
                sh 'echo PHP Syntax check'
                /*
                sh 'vendor/bin/parallel-lint web/modules/custom'
                */
            }
        }
        stage('Test'){
            steps {
                sh 'cd web && (../vendor/bin/drush runserver http://127.0.0.1:8888 &) && php ./core/scripts/run-tests.sh --dburl sqlite://tmp/tests.sqlite --sqlite /tmp/tests.sqlite --url http://127.0.0.1:8888/ --concurrency 4 Batch'
                /*
                sh 'vendor/bin/phpunit -c build/phpunit.xml || exit 0'
                step([
                    $class: 'XUnitBuilder',
                    thresholds: [[$class: 'FailedThreshold', unstableThreshold: '1']],
                    tools: [[$class: 'JUnitType', pattern: 'build/logs/junit.xml']]
                ])
                publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'build/coverage', reportFiles: 'index.html', reportName: 'Coverage Report', reportTitles: ''])
                */
                /* BROKEN step([$class: 'CloverPublisher', cloverReportDir: 'build/coverage', cloverReportFileName: 'build/logs/clover.xml']) */
                /* BROKEN step([$class: 'hudson.plugins.crap4j.Crap4JPublisher', reportPattern: 'build/logs/crap4j.xml', healthThreshold: '10']) */
            }
        }
        stage('Checkstyle') {
            steps {
                sh 'phpcs --standard=Drupal -e'
                sh 'phpcs --standard=Drupal --extensions=php,module,inc,install,test,profile,theme,css,info,txt,md --ignore=node_modules,bower_components,vendor web/modules/custom || exit 0'
                checkstyle pattern: 'build/logs/checkstyle.xml'
            }
        }
        stage('Lines of Code') { steps { sh 'vendor/bin/phploc --count-tests --exclude vendor/ --log-csv build/logs/phploc.csv --log-xml build/logs/phploc.xml .' } }
        stage('Copy paste detection') {
            steps {
                sh 'vendor/bin/phpcpd --log-pmd build/logs/pmd-cpd.xml --exclude vendor . || exit 0'
                dry canRunOnFailed: true, pattern: 'build/logs/pmd-cpd.xml'
            }
        }
        /* -- SLOW
        stage('Mess detection') {
            steps {
                sh 'vendor/bin/phpmd . xml build/phpmd.xml --reportfile build/logs/pmd.xml --exclude vendor/ || exit 0'
                pmd canRunOnFailed: true, pattern: 'build/logs/pmd.xml'
            }
        }
        stage('Software metrics') { steps { sh 'vendor/bin/pdepend --jdepend-xml=build/logs/jdepend.xml --jdepend-chart=build/pdepend/dependencies.svg --overview-pyramid=build/pdepend/overview-pyramid.svg --ignore=vendor .' } }
        stage('Generate documentation') { steps { sh 'vendor/bin/phpdox -f build/phpdox.xml' } }
        */
    }
}